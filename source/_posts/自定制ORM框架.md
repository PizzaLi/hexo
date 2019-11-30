---
title: 自定制ORM框架
date: 2019-01-24 17:03:57
tags:
 - ORM
 - Django
---

下面是基于元编程和descriptor实现的自定制ORM框架源码，详细介绍会在近期发布，敬请期待

<!--more-->

```
# -*- coding: utf-8 -*-


class Field(object):

    def __init__(self, name, column_type, required, field_type):
        self.name = name
        self.column_type = column_type
        self.required = required
        self.field_type = field_type

    def __str__(self):
        return '<%s:%s>' % (self.__class__.__name__, self.name)


class StringField(Field):

    def __init__(self, name, required=False, field_type=str):
        super(StringField, self).__init__(name, 'varchar(100)', required,
                field_type)


class IntegerField(Field):

    def __init__(self, name, required=False, field_type=int):
        super(IntegerField, self).__init__(name, 'int', required, field_type)


class ModelMetaclass(type):

    def __new__(cls, name, bases, attrs):
        if name=='Model':
            return type.__new__(cls, name, bases, attrs)
        mappings = dict()
        requireds = dict()
        field_type = dict()
        for k, v in attrs.items():
            if isinstance(v, Field):
                mappings[k] = v
                field_type[k] = v.field_type
                if v.required:
                    requireds[k] = 'required'
        for k in mappings.keys():
            attrs.pop(k)
        attrs['__mappings__'] = mappings
        attrs['__table__'] = name
        attrs['__requireds__'] = requireds
        attrs['__field_type__'] = field_type
        return type.__new__(cls, name, bases, attrs)


class Model(dict, metaclass=ModelMetaclass):

    def __init__(self, **kw):
        self.fields = kw
        if self.check_fields():
            super(Model, self).__init__(**kw)

    def __getattr__(self, key):
        try:
            return self[key]
        except KeyError:
            raise AttributeError(r"'Model' object has no attribute '%s'" % key)

    def __setattr__(self, key, value):
        self[key] = value

    def check_fields(self):
        kw = self.fields
        required_fields = set(type(self).__dict__['__requireds__'])
        applied_fields = set(kw)
        missed_fields = required_fields - applied_fields
        field_type = type(self).__dict__['__field_type__']
        if len(missed_fields) == 0:
            for k, v in kw.items():
                if isinstance(kw[k], field_type[k]):
                    continue
                else:
                    raise TypeError('Field type error')
            return True
        else:
            raise ValueError('Field %s required' % missed_fields)

    def save(self):
        fields = []
        params = []
        args = []
        for k, v in self.__mappings__.items():
            fields.append(v.name)
            params.append('?')
            args.append(getattr(self, k, None))
        sql = 'insert into %s (%s) values (%s)' % (self.__table__,
                ', '.join(fields), ', '.join(str(arg) for arg in args))
        print('SQL: %s' % sql)
        print('ARGS: %s' % str(args))


class User(Model):
    uid = IntegerField('id')
    name = StringField('username', required=True)
    email = StringField('email')
    password = StringField('password', required=True)


if __name__ == '__main__':
    u = User(uid=10001, name='Joe', email='example@example.com', password='10001')
    u.save()
```

