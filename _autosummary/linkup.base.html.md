# linkup.base

Base functions and classes for linked/joined operations.

### Functions

| `gen_attrname_func_for_module`(module[, ...])                                                    |                                                                                                                |
|--------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------|
| [`key_aligned_val_op`](#linkup.base.key_aligned_val_op)(x, y, op[, ...])             |                                                                                                                |
| [`key_aligned_val_op_with_forced_defaults`](#linkup.base.key_aligned_val_op_with_forced_defaults)(x, ...) | Apply an operator to the key-aligned values of two dictionaries, using specified defaults for each dictionary. |
| [`map_op_val`](#linkup.base.map_op_val)(x, val, op[, items_to_mapping])      | Apply operation op(v, val) to every value v of mapping x.                                                      |
| `startswith_dunder`(x)                                                                           |                                                                                                                |

### Classes

| [`OperableMapping`](#linkup.base.OperableMapping)        |                                                                              |
|-------------------------------------------------------------------------|------------------------------------------------------------------------------|
| [`OperableMappingNoDflts`](#linkup.base.OperableMappingNoDflts) | OperableMapping with ALL operators of operator module (but without defaults) |

### *class* linkup.base.OperableMapping

Bases: [`dict`](https://docs.python.org/3/builtins/stdtypes.html#dict)

```pycon
>>> d = OperableMapping({'a': 8, 'b': 4})
>>> dd = OperableMapping(b=2, c=1)  # you can make one this way too
>>> d + dd
{'a': 8, 'b': 6, 'c': 1}
>>> d - dd
{'a': 8, 'b': 2, 'c': -1}
>>> d * dd
{'a': 8, 'b': 8, 'c': 1}
>>> d / dd
{'a': 8.0, 'b': 2.0, 'c': 1.0}
```

The result of the operations are themselves DictWithOps, so you can compose several.

```pycon
>>> d * (dd + d) / d  # notice that this is equivalent to d + dd (but with numbers cast to floats)
{'a': 8.0, 'b': 6.0, 'c': 1.0}
```

You can also use values (which will have the effect of being broadcast to all values of the mapping.

```pycon
>>> d + 1
{'a': 9, 'b': 5}
>>> d * 10
{'a': 80, 'b': 40}
```

### *class* linkup.base.OperableMappingNoDflts

Bases: [`dict`](https://docs.python.org/3/builtins/stdtypes.html#dict)

OperableMapping with ALL operators of operator module (but without defaults)

```pycon
>>> from linkup.base import *
>>> d = OperableMappingNoDflts({'a': 8, 'b': 4, 'c': 3})
>>> dd = OperableMappingNoDflts(b=2, c=1, d=0)  # you can make one this way too
>>>
>>> d + 1
{'a': 9, 'b': 5, 'c': 4}
>>> d / dd
{'b': 2.0, 'c': 3.0}
>>> (d + 1) / dd
{'b': 2.5, 'c': 4.0}
```

### linkup.base.key_aligned_val_op(x, y, op, dflt_val_for_x=<object object>, dflt_val_for_y=<object object>, empty_mapping_factory=<class 'dict'>)

```pycon
>>> from operator import add, sub, mul, truediv, and_, or_, xor
>>> x = {'a': 8, 'b': 4}
>>> y = {'b': 2, 'c': 1}
```

If no default vals are given, only those keys that are in both mappings will be in the output.

```pycon
>>> key_aligned_val_op(x, y, add)
{'b': 6}
```

If we specify a default for `x` then all items of `y` can be used.

```pycon
>>> key_aligned_val_op(x, y, add, dflt_val_for_x=0)
{'b': 6, 'c': 1}
```

If we specify a default for `y` then all items of `x` can be used.

```pycon
>>> key_aligned_val_op(x, y, add, dflt_val_for_y=0)
{'a': 8, 'b': 6}
```

### linkup.base.key_aligned_val_op_with_forced_defaults(x, y, op, dflt_val_for_x, dflt_val_for_y, empty_mapping_factory=<class 'dict'>)

Apply an operator to the key-aligned values of two dictionaries, using specified defaults for each dictionary.

The output’s keys will be the union of the keys of the input dictionaries.

The type of the output will be the type of that is returned by empty_mapping_factory.

```pycon
>>> from operator import add, sub, mul, truediv, and_, or_, xor
>>> x = {'a': 8, 'b': 4}
>>> y = {'b': 2, 'c': 1}
>>> key_aligned_val_op_with_forced_defaults(x, y, add, 0, 0)
{'a': 8, 'b': 6, 'c': 1}
>>> key_aligned_val_op_with_forced_defaults(x, y, sub, 0, 0)
{'a': 8, 'b': 2, 'c': -1}
>>> key_aligned_val_op_with_forced_defaults(x, y, mul, 1, 1)
{'a': 8, 'b': 8, 'c': 1}
>>> key_aligned_val_op_with_forced_defaults(x, y, truediv, 1, 1)
{'a': 8.0, 'b': 2.0, 'c': 1.0}
>>> x = {'a': [8], 'b': [4]}
>>> y = {'b': [2], 'c': [1]}
>>> key_aligned_val_op_with_forced_defaults(x, y, add, [], [])
{'a': [8], 'b': [4, 2], 'c': [1]}
>>> x = {'a': True, 'b': False}
>>> y = {'b': True, 'c': False}
>>> key_aligned_val_op_with_forced_defaults(x, y, and_, True, True)
{'a': True, 'b': False, 'c': False}
>>> key_aligned_val_op_with_forced_defaults(x, y, or_, True, True)
{'a': True, 'b': True, 'c': True}
>>> key_aligned_val_op_with_forced_defaults(x, y, xor, True, True)
{'a': False, 'b': True, 'c': True}
```

* **Return type:**
  [`Mapping`](https://docs.python.org/3/library/collections.abc.html#collections.abc.Mapping)

### linkup.base.map_op_val(x, val, op, items_to_mapping=<class 'dict'>)

Apply operation op(v, val) to every value v of mapping x.

```pycon
>>> from operator import add, sub, mul, truediv
>>> x = dict(a=2, b=3)
>>> map_op_val(x, 2, add)
{'a': 4, 'b': 5}
>>> map_op_val(x, 2, sub)
{'a': 0, 'b': 1}
>>> map_op_val(x, 2, mul)
{'a': 4, 'b': 6}
>>> map_op_val(x, 2, truediv)
{'a': 1.0, 'b': 1.5}
```
