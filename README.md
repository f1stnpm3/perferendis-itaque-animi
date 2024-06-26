In a Nutshell
=============

This is a command line option parser that tries not to outsmart the
user, but still tries to get the job done.


Example
=======

```
const Optist = require('@f1stnpm3/perferendis-itaque-animi');

var opt = ((new Optist())
           .simple('a')
           .simple('b')
           .counter('c')
           .simple('d', ['dddddd', 'dddddddd', 'DDD'])
           .string('s', 'string-of-3-chars', /^.{3}$/)
           .multi('m', 'multi-opt')
           .opts([ { shortName: ['A', 'O'], longName: 'all', description: 'It is A and O' },
                   { shortName: 'S', longName: 'required-string', hasArg: true, required: true } ])
           .help('test')
           .parse());

console.log('a', opt.value('a'));
console.log('b', opt.value('b'));
console.log('c', opt.value('c'));
console.log('d', opt.value('d'));
console.log('m', opt.value('m'));
console.log(opt.rest());

```


Reference
=========

Optist()
--------

Constructor for a command line parser. Does not take arguments.

Optist.prototype.simple(shortName, longName)
--------------------------------------------

An alias of:
```
Optist.prototype.o(shortName, longName, false, false, undefined, false, undefined);
```

This is a simple option that can either be passed once or omitted in
the argument list. The value of the option is true if it's present in
the argument list, and false otherwise.

Optist.prototype.counter(shortName, longName)
---------------------------------------------

An alias of:
```
Optist.prototype.o(shortName, longName, false, false, undefined, true, undefined);
```

This is an option without arguments which can be passed multiple
times. The value of the option is the number of times it was present
in the argument list.

Optist.prototype.string(shortName, longName, allowedValues)
-----------------------------------------------------------

A simple single-shot option with an optional set of allowed
values. Parameter allowedValues can be either a single string or
RegExp object or array of strings or RegExp objects or a mixed array
of both. If the option is passed, the parsing fails in case the option
argument does not match to any of the allowed values.

Without allowedValues parameter, this function is alias of:
```
Optist.prototype.o(shortName, longName, true, false, undefined, false, undefined);

```

Optist.prototype.multi(shortName, longName)
-------------------------------------------

An alias of:
```
Optist.prototype.o(shortName, longName, true, false, undefined, true, undefined);
```

This is an option with an argument that can be passed multiple
times. All passed arguments are collected in an array and are
available as a value after the parsing. If the option is not present
in the argument list at all, then the value is an empty array.

Optist.prototype.describeOpt = function(name, description)
----------------------------------------------------------

Attach a free form text description for an option. This can be done
only once for each individual option.

Optist.prototype.describeParam(num, name, description)
------------------------------------------------------

Attach a name and a free form text description for an additional
command line parameter. This can be done only once for each individual
parameter. Parameters are numbered from 0 up.

Optist.prototype.attachOptArgCb(name, optArgCb)
-----------------------------------------------

Attach an argument processing callback to the previously defined
option. The option can be referred by any of its aliases, short or
long. This function can not be called, if optArgCb has already been
set for the function.

Optist.prototype.o(shortName, longName, hasArg, required, defaultValue, multi, optArgCb, requiresAlso, conflictsWith, environment)
----------------------------------------------------------------------------------------------------------------------------------

A full featured interface for option definition.

### shortName
A single character string giving the short name of the option. Can't
be a dash (-) or an equal sign (=). Can be an array of multiple short
aliases of the same option.

### longName
A multicharacter string giving the long name of the option. Can't be a
single character and can't contain an equal sign (=). The first or the
last character can't be a dash (-). Can be an array of multiple long
aliases of the same option.

### hasArg
If false, the option does not accept an argument. If true,
the argument must be passed.

### required
If true, the option must be present in the argument list. For the
required option, it is not possible to give a default value.

### defaultValue
A default value for an option in case it's not present. The default
value must _always_ be a string. In case there is an optArgCb for the
option, it must be able to process also this default value. A default
value can only be set for options accepting an argument.

### multi
If true, the option can appear multiple times in the argument list.
If the option does not accept an argument, it makes the option a
counter, i.e. the value of the option after the parsing, is the number
of times the option was present in the command line. If the option
accepts the argument, it makes the value of the option after parsing
an array containing all arguments given to the option in the order
they were given or in case the option is not present at all either an
empty array or if there is a default value, then an array containig
only the default value.

If not true, the option can only appear once in the argument list.

### optArgCb

An processing callback a command line option argument. This can only
be defined for options accepting an argument. The option argument is
always passed as string to the callback and the callback is expected
to return the argument in a processed form. The returned value does
not have to be a string but can be anything except undefined. If the
processing of the argument fails, the callback should return
undefined, which triggers an error in command line parsing.

Since this callback is always also called for possible default values
of the option, side effects are not desireable.

Pre-made callbacks are available in util module of the @f1stnpm3/perferendis-itaque-animi package.

### requiresAlso

A single string or array of strings listing options that are also
required if this option is present.

### conflictsWith

A single string or array of strings listing options that must not be
present if this option is present.

### environment

If the option is not given on the command line, read its value from
the environment with a given name. For options without argument, the
allowed values are 'yes' and 'no' (with aliases 'true'/'false' and
'+'/'-' respectively).

Optist.prototype.opts(options)
------------------------------

A shortcut method to define multiple options. Each option definition
is an object with properties from list: shortName, longName, hasArg,
required, defaultValue, multi, optArgCb, description. See
Optist.prototype.o() for definition of each property.

Optist.prototype.additional(restRequireMin, restRequireMax)
-----------------------------------------------------------

Set the minimum and/or maximum number of additional parameters that
must be left in the argument list after the options have been
parsed. This can be done only before parsing and can be set only once.
Alternatively the limits can be set also with additional function
arguments for parse/parsePosix methods, but it is recommended to use
Optist.prototype.additional API instead, because the information can
in future, also be enclosed into usage messages.

Optist.prototype.help(command)
------------------------------

Enable the automatic help message generation and attach it to command
line options -h and --help. Those options must not be otherwise
attached. In case of parsing error of command line options, the help
message is automatically shown and program terminated with non-zero
exit code. This function can only be called before parsing.

If parameter command is given, the program name is oveddidden in the
help messages. Command defaults to:
```
fs.basename(process.argv[0]) + ' ' + fs.basename(process.argv[1])
```

Optist.prototype.parse(av, restRequireMin, restRequireMax)
----------------------------------------------------------

Parse the argument list. The parsing stops when the first non-option
argument is detected or when the parsing is explicitly stopped by two
dashes '--' in the argument list. This is the sane way. The rest of
the argument list can be retrieved using Optist.prototype.rest()
interface. The parameters restRequireMin and restRequireMax can be
used in order to set constraints for the number of arguments that must
be left in the argument list, after the parsing has completed.

If the argument list av is omitted or set to undefined, it defaults to
process.argv.slice(2).

Optist.prototype.parsePosix(av, restRequireMin, restRequireMax)
---------------------------------------------------------------

Parse the argument list in weird but kinda Posixly standard way.  The
parsing stops, if it's explicitly stopped by two dashes '--' in the
argument list. Otherwise, non-option arguments are skipped and the
parsing resumed until entire argument list has been
processed. Whatever was skipped on the way, can be retrieved using
Optist.prototype.rest() interface. In my opinion, this is totally
bonkers, but in case this is how you like it, so be it. Otherwise this
interface acts like Optist.prototype.parse().

Optist.prototype.rest()
-----------------------

Return whatever is left of the argument list after the options have
been parsed and removed. This function always returns a new array, so
the caller modifying the array is ok. This function can be called
multiple times and a new array is returned each time.

Optist.prototype.value(name)
----------------------------

Lookup the value of an option after parsing the command line. The name
of the option can be given either as a long or short option name. If
the option has multiple aliases, all names return the same value.

Optist.prototype.values()
-------------------------

Return all option values agter parsing the command line. Return value
is an object with all aliases of the options as properties and a
corresponding option value as a value of each property. Options that
have no value given in the command line or does not have a default
value, are omitted. The exception is a multi type options that are
either [] or 0 depending on whether they accept an argument or not.

In addition, the non-option argument list (as returned with
Optist.prototype.rest()) is stored to the object with property name
consisting of a single dash (i.e. '-').

Optist.prototype.forEach(cb)
----------------------------

Iterate over the defined options after parsing and call a caller
provided callback for each option. Parameters passed to the callback
are primary short option name, primary long option name and option
value. If option was not given on the command line and has no default
value, the value is then passed as undefined. Also if the short or
long option name does not exist, it is passed as undefined.

```
opt.forEach(function(s, l, v) {
  console.log('short:', s, 'long:', l, 'value:, v);
});
```

Utilities
=========

Some generic argument callbacks are provided by util library.
Those functions can be passed as optArgCb in option definition.
User can of course also define custom callbacks.

```
const Optist = require('@f1stnpm3/perferendis-itaque-animi');
const ou = require('@f1stnpm3/perferendis-itaque-animi/util');

var opt = ((new Optist())
           .o(undefined, 'integer-option', true, false, '42', false, ou.integerCb)
           .parse())
console.log('integer option:',
            opt.value('integer-option'),
            'of type',
            typeof(opt.value('integer-option')));
```


Author
======

Timo J. Rinne <tri@iki.fi>


License
=======

MIT License
