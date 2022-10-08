Shared Integer Cache for PHP
============================


The extension offers a set of operations to store an integer value
in the shared memory, associated with a name (string identifier).
Each value is to be atomically changed.

Supported versions of PHP: 7.0+, 8.0+


## The story

The extension appeared as during tackling with Lock Contention in APCu.

It allocates a number of segments in shared memory, each segment gets its own lock
to perform the operation.

Each value may have its own TTL or live forever

## Build

Assuming that PHP is already installed on the system

```bash
git clone git@github.com:bumble-tech/php-sic.git
cd php-sic
phpize
./configure
make
make test
```

## Setup

INI records related to the extension

```ini

# Attach the extension in the runtime
extension=sic.so

; Enable/disable the extension
;
; for the moment, it can only be set in PHP_INI_SYSTEM
;
; Boolean: 1/0/on/off/true/false
; Default Value: off
sic.enabled = on

; The number of "segments" to create on startup
;
; Integer: should be > 0
; Default Value: 10
sic.shard_num = 10

; The segment size
;
; Memory: 1M/100K/etc...
; Default Value: 1M
sic.shard_size=200K
```


## Functions

### bool sic_set(string key, int value[, int ttl])

Forcibly sets the value


#### Agruments

**key** *string*, the key associated with the value\
**value** *int*, the new value to set\
**ttl** *int*, time to live in seconds


#### Return value

**true** on success, **false** otherwise


### bool sic_add(string key, int value[, int ttl])

Creates a value in the storage. Fails if a value with such a name already exists.

#### Agruments

**key** *string*, the key associated with the value\
**value** *int*, the new value to set\
**ttl** *int*, time to live in seconds


#### Return value

**true** on success, **false** otherwise


### bool sic_del(string key)

Removes the value from the storage. Fails if the value doesn't exists.

#### Agruments

**key** *string*, the key associated with the value\


#### Return value

**true** on success, **false** otherwise


### int|false sic_get(string key)

Retreives the value from the storage

#### Agruments

**key** *string*, the key associated with the value


#### Return value

**false** if error occured or the value does not exists, **int** - the value, otherwise


### bool sic_exists(string key)

Checks whether the value exists in the storage

#### Agruments

**key** *string*, the key associated with the value


#### Return value

**false** if error occured or the value does not exists, **true** otherwise



### int|false sic_inc(string key[, int inc_value[, int ttl]])

Increments the value, fails if such a key does not exist

#### Agruments

**key** *string*, the key associated with the value\
**value** *int*, value of increment\
**ttl** *int*, time to live in seconds


#### Return value

**false** if error occured or the value does not exists, **int** - the new value otherwise


### int|false sic_dec(string key[, int inc_value[, int ttl]])

Increments the value, fails if such a key does not exist

#### Agruments

**key** *string*, the key associated with the value\
**value** *int*, value of decrement\
**ttl** *int*, time to live in seconds


#### Return value

**false** if error occured or the value does not exists, **int** - the new value otherwise



### bool sic_cas(string key, int old_value, int new_value)

Updates the value using Compare-And-Swap semantic. Updates the value if the current value is expected (== **old_value**)

#### Agruments

**key** *string*, the key associated with the value\
**old_value** *int*, expected value\
**new_value** *int*, the new value to set 


#### Return value

**false** if error occured or the value does not exists, **int** - the new value otherwise


### bool sic_gc()

Runs Garbadge Collection

#### Return value

**false** if error occured




### array sic_info()

Returns info about segments, an element for each segment:


**size** *int* the size of the segment in bytes\
**unused_size** *int* how much memory is not used yet in the segment, bytes\
**used_cnt** *int* number of stored items\
**used_data_size** *int* how much memory is used, bytes\
**frag_err_cnt** *int* \
**oom_err_cnt** *int*

