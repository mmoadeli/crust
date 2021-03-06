### Definitions

#### Platform specific paths

##### UserAppDir

 - *Windows*: `env::var("APPDATA") / ExeName()`
 - *APPLE*: `env::home_dir() / "Library/Application Support" / ExeName()`
 - *Linux*:  `env::home_dir() / ".config" / ExeName()`

##### SystemAppSupportDir

 - *Windows*: `env::var("ALLUSERSPROFILE") / ExeName()`
 - *Apple*: `"/Library/Application Support/" / ExeName()`
 - *Linux*: `"/var/lib" / ExeName()`


### Configuration file path
#### Naming convention
File name needs to be of the format : `[current_executable_name].crust.config`
For crust_peer example, the config file name will be `crust_peer.crust.config`


####Reading order
On construction of `crust::connection_manager` instance, crust will try to read configuration file from the following path in same order.

1. Current executable directory: using `std::env::current_exe`
2. Current user's application directory:  `UserAppDir`
3. Application support directory for all users: `SystemAppSupportDir `


Pro`s:
- Intermediate layer libraries need not to know anything about networking configuration.
- Installers can create the config file at appropriate path with desired name and permissions.
- Tests can override other paths by placing config file in current exe directory.
- File name format helps in separating config file of different application.
- Using `std::env::current_exe` against working directory helps in cleanup as config files gets created on fixed location(s). Example: running test file from multiple location will create multiple config files and cleanup is difficult.

Cons:
- Running parallel tests will interfere each other. [This can be addressed by using memory mapped file.]
- Multiple instances of same application cannot have customised config option per instance.
- Any user can override existing `SystemAppSupportDir ` config file by creating `UserAppDir`.[User can only pollute Vaults running under current user only]

#### Writing order

In case the configuration file is missing at all above paths, crust will **NOT**
attempt to create file, but use a default instead. If explicitly requested,
config file will be written at current executable directory (using
`std::env::current_exe`).

##### Pros

- Application's advanced users can modify config easily by altering the file.

### Bootstrap cache file path

Same conventions as config file will be used for bootstrap cache file as well.

#### Naming convention
File name needs to be of the format : `[current_executable_name].crust.bootstrap.cache`
For crust_peer example, the config file name will be `crust_peer.crust.bootstrap.cache`

#### Reading order

On construction of `crust::connection_manager` instance, crust will try to read
bootstrap cache file from the following path in same order.

1. Current executable directory: using `std::env::current_exe`
2. Current user's application directory:  `UserAppDir`
3. Application support directory for all users: `SystemAppSupportDir `

#### Writing order

In case the bootstrap cache file is missing at all above paths, crust will
attempt to create file at following path in same order.

1. Application support directory for all users: `SystemAppSupportDir `
2. Current user's application directory:  `UserAppDir`
3. Current executable directory: using `std::env::current_exe`
