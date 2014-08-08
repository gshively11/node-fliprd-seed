node-fliprd-seed
================

Command line tool for sending fliprd configuration files to etcd.

# Work In Progress (Notes)

##Example Usages
```
fliprd-seed --host 127.0.0.1 --port 4001 --dir /path/to/config --key myapp --single
```
```
fliprd-seed --host 127.0.0.1 --port 4001 --dir /path/to/config --keys myapp:somefolder,otherapp:otherfolder
```
###Args
* `--single` or `s`: indicates that `--dir` points to a single app's config.  Uses final folder in `dir` path for key if `key` not specified.  If `--single` is not specified, it assumes that `--dir` contains folders for multiple apps.
* `--keys`: or `-ks` will accept key:folder[,key:folder] mappings.  only used if `--single` is not specified
* `--norules`: or `-nr` will skip rule validation
* `--noenv`: or `-ne` will no longer consider filenames as environments and compile everything into a single config, making it accessible at /flipr/key
* `--novalidate`: or `-nv` will skip validation
* `--host` or `-h` etcd host
* `--port` or `-p` etcd port
* `--dir` or `-d` directory containing app/organization folders and config files
 
##Behavior
* Any folders inside app folders are solely for organization.  You can name them whatever you want, and can be as nested as you like.
* File names are reserved to specify environment.
  * This means you need to use folders if you want to split a single environment's config into multiple files.
* When run, it will look recursively in dir and compile a single config for each environment found.
* For each key, it will call out to etcd at /fliprd/key_rules to grab any rules defined by the app (unless `--norules` is used)
* It will then validate all the configs (using rules if available).  If validation fails, it will output an error and skip that key.
* If the configs for an app are vaild, it will put the json serialized config for each environment at /fliprd/key/env
