![Composer](Composer.jpg)

***Composer*** is a tool for dependency management in PHP. It allows you to declare the libraries your project depends on and it will manage (install/update) them for you.

---

## Dependency management

Composer is **not** a package manager in the same sense as Yum or Apt are. Yes,
it deals with "packages" or libraries, but it manages them on a per-project
basis, installing them in a directory (e.g. `vendor`) inside your project. By
default, it does not install anything globally. Thus, it is a dependency
manager. It does however support a "global" project for convenience via the
[global](03-cli.md#global) command.

This idea is not new and Composer is strongly inspired by node's
[npm](https://www.npmjs.com/) and ruby's [bundler](https://bundler.io/).

Suppose:

1. You have a project that depends on a number of libraries.
2. Some of those libraries depend on other libraries.

Composer:

1. Enables you to declare the libraries you depend on.
2. Finds out which versions of which packages can and need to be installed, and
   installs them (meaning it downloads them into your project).
3. You can update all your dependencies in one command.

See the [Basic usage](01-basic-usage.md) chapter for more details on declaring
dependencies.

---

## System Requirements

Composer requires PHP 5.3.2+ to run. A few sensitive php settings and compile
flags are also required, but when using the installer you will be warned about
any incompatibilities.

To install packages from sources instead of plain zip archives, you will need
git, svn, fossil or hg depending on how the package is version-controlled.

Composer is multi-platform and we strive to make it run equally well on Windows,
Linux and macOS.

---

## Installation - Linux / Unix / macOS

### Downloading the Composer Executable

Composer offers a convenient installer that you can execute directly from the
command line. Feel free to [download this file](https://getcomposer.org/installer)
or review it on [GitHub](https://github.com/composer/getcomposer.org/blob/master/web/installer)
if you wish to know more about the inner workings of the installer. The source
is plain PHP.

There are in short, two ways to install Composer. Locally as part of your
project, or globally as a system wide executable.

#### Locally

To install Composer locally, run the installer in your project directory. See
[the Download page](https://getcomposer.org/download/) for instructions.

The installer will check a few PHP settings and then download `composer.phar`
to your working directory. This file is the Composer binary. It is a PHAR
(PHP archive), which is an archive format for PHP which can be run on
the command line, amongst other things.

Now run `php composer.phar` in order to run Composer.

You can install Composer to a specific directory by using the `--install-dir`
option and additionally (re)name it as well using the `--filename` option. When
running the installer when following
[the Download page instructions](https://getcomposer.org/download/) add the
following parameters:

```sh
php composer-setup.php --install-dir=bin --filename=composer
```

Now run `php bin/composer` in order to run Composer.

#### Globally

You can place the Composer PHAR anywhere you wish. If you put it in a directory
that is part of your `PATH`, you can access it globally. On Unix systems you
can even make it executable and invoke it without directly using the `php`
interpreter.

After running the installer following [the Download page instructions](https://getcomposer.org/download/)
you can run this to move composer.phar to a directory that is in your path:

```sh
mv composer.phar /usr/local/bin/composer
```

If you like to install it only for your user and avoid requiring root permissions,
you can use `~/.local/bin` instead which is available by default on some
Linux distributions.

> **Note:** If the above fails due to permissions, you may need to run it again
> with sudo.

> **Note:** On some versions of macOS the `/usr` directory does not exist by
> default. If you receive the error "/usr/local/bin/composer: No such file or
> directory" then you must create the directory manually before proceeding:
> `mkdir -p /usr/local/bin`.

> **Note:** For information on changing your PATH, please read the
> [Wikipedia article](https://en.wikipedia.org/wiki/PATH_(variable)) and/or use
> your search engine of choice.

Now run `composer` in order to run Composer instead of `php composer.phar`.

---

## Installation - Windows

### Using the Installer

This is the easiest way to get Composer set up on your machine.

Download and run
[Composer-Setup.exe](https://getcomposer.org/Composer-Setup.exe). It will
install the latest Composer version and set up your PATH so that you can
call `composer` from any directory in your command line.

> **Note:** Close your current terminal. Test usage with a new terminal: This is
> important since the PATH only gets loaded when the terminal starts.

### Manual Installation

Change to a directory on your `PATH` and run the installer following
[the Download page instructions](https://getcomposer.org/download/)
to download `composer.phar`.

Create a new `composer.bat` file alongside `composer.phar`:

Using cmd.exe:

```sh
C:\bin> echo @php "%~dp0composer.phar" %*>composer.bat
```

Using PowerShell:

```sh
PS C:\bin> Set-Content composer.bat '@php "%~dp0composer.phar" %*'
```

Add the directory to your PATH environment variable if it isn't already.
For information on changing your PATH variable, please see
[this article](https://www.computerhope.com/issues/ch000549.htm) and/or
use your search engine of choice.

Close your current terminal. Test usage with a new terminal:

```sh
C:\Users\username>composer -V
Composer version 1.0.0 2016-01-10 20:34:53
```

---

## Using Composer

Now that you've installed Composer, you are ready to use it! Head on over to the
next chapter for a short demonstration.

[Basic usage](01-basic-usage.md) &rarr;

---

# Basic usage

## Introduction

For our basic usage introduction, we will be installing `monolog/monolog`,
a logging library. If you have not yet installed Composer, refer to the
[Intro](00-intro.md) chapter.

> **Note:** for the sake of simplicity, this introduction will assume you
> have performed a [local](00-intro.md#locally) install of Composer.

---

## `composer.json`: Project setup

To start using Composer in your project, all you need is a `composer.json`
file. This file describes the dependencies of your project and may contain
other metadata as well. It typically should go in the top-most directory of
your project/VCS repository. You can technically run Composer anywhere but
if you want to publish a package to Packagist.org, it will have to be able
to find the file at the top of your VCS repository.

### The `require` key

The first (and often only) thing you specify in `composer.json` is the
[`require`](04-schema.md#require) key. You are telling Composer which
packages your project depends on.

```json
{
    "require": {
        "monolog/monolog": "1.0.*"
    }
}
```

As you can see, [`require`](04-schema.md#require) takes an object that maps
**package names** (e.g. `monolog/monolog`) to **version constraints** (e.g.
`1.0.*`).

Composer uses this information to search for the right set of files in package
"repositories" that you register using the [`repositories`](04-schema.md#repositories)
key, or in Packagist, the default package repository. In the above example,
since no other repository has been registered in the `composer.json` file, it is
assumed that the `monolog/monolog` package is registered on Packagist. (See more
about Packagist [below](#packagist), or read more about repositories
[here](05-repositories.md)).

### Package names

The package name consists of a vendor name and the project's name. Often these
will be identical - the vendor name only exists to prevent naming clashes. For
example, it would allow two different people to create a library named `json`.
One might be named `igorw/json` while the other might be `seldaek/json`.

Read more about publishing packages and package naming [here](02-libraries.md).
(Note that you can also specify "platform packages" as dependencies, allowing
you to require certain versions of server software. See
[platform packages](#platform-packages) below.)

### Package version constraints

In our example, we are requesting the Monolog package with the version constraint
[`1.0.*`](https://semver.mwl.be/#?package=monolog%2Fmonolog&version=1.0.*).
This means any version in the `1.0` development branch, or any version that is
greater than or equal to 1.0 and less than 1.1 (`>=1.0 <1.1`).

Please read [versions](articles/versions.md) for more in-depth information on
versions, how versions relate to each other, and on version constraints.

> **How does Composer download the right files?** When you specify a dependency in
> `composer.json`, Composer first takes the name of the package that you have requested
> and searches for it in any repositories that you have registered using the
> [`repositories`](04-schema.md#repositories) key. If you have not registered
> any extra repositories, or it does not find a package with that name in the
> repositories you have specified, it falls back to Packagist (more [below](#packagist)).
>
> When Composer finds the right package, either in Packagist or in a repo you have specified,
> it then uses the versioning features of the package's VCS (i.e., branches and tags)
> to attempt to find the best match for the version constraint you have specified. Be sure to read
> about versions and package resolution in the [versions article](articles/versions.md).

> **Note:** If you are trying to require a package but Composer throws an error
> regarding package stability, the version you have specified may not meet your
> default minimum stability requirements. By default, only stable releases are taken
> into consideration when searching for valid package versions in your VCS.
>
> You might run into this if you are trying to require dev, alpha, beta, or RC
> versions of a package. Read more about stability flags and the `minimum-stability`
> key on the [schema page](04-schema.md).

---

## Installing dependencies

To install the defined dependencies for your project, run the
[`install`](03-cli.md#install-i) command.

```sh
php composer.phar install
```

When you run this command, one of two things may happen:

### Installing without `composer.lock`

If you have never run the command before and there is also no `composer.lock` file present,
Composer resolves all dependencies listed in your `composer.json` file and downloads
the latest version of their files into the `vendor` directory in your project. (The `vendor`
directory is the conventional location for all third-party code in a project). In our
example from above, you would end up with the Monolog source files in
`vendor/monolog/monolog/`. If Monolog listed any dependencies, those would also be in
folders under `vendor/`.

> **Tip:** If you are using git for your project, you probably want to add
> `vendor` in your `.gitignore`. You really don't want to add all of that
> third-party code to your versioned repository.

When Composer has finished installing, it writes all of the packages and the exact versions
of them that it downloaded to the `composer.lock` file, locking the project to those specific
versions. You should commit the `composer.lock` file to your project repo so that all people
working on the project are locked to the same versions of dependencies (more below).

### Installing with `composer.lock`

This brings us to the second scenario. If there is already a `composer.lock` file as well as a
`composer.json` file when you run `composer install`, it means either you ran the
`install` command before, or someone else on the project ran the `install` command and
committed the `composer.lock` file to the project (which is good).

Either way, running `install` when a `composer.lock` file is present resolves and installs
all dependencies that you listed in `composer.json`, but Composer uses the exact versions listed
in `composer.lock` to ensure that the package versions are consistent for everyone
working on your project. As a result you will have all dependencies requested by your
`composer.json` file, but they may not all be at the very latest available versions
(some of the dependencies listed in the `composer.lock` file may have released newer versions since
the file was created). This is by design, it ensures that your project does not break because of
unexpected changes in dependencies.

### Commit your `composer.lock` file to version control

Committing this file to VC is important because it will cause anyone who sets
up the project to use the exact same
versions of the dependencies that you are using. Your CI server, production
machines, other developers in your team, everything and everyone runs on the
same dependencies, which mitigates the potential for bugs affecting only some
parts of the deployments. Even if you develop alone, in six months when
reinstalling the project you can feel confident the dependencies installed are
still working even if your dependencies released many new versions since then.
(See note below about using the `update` command.)

> **Note:** For libraries it is not necessary to commit the lock
> file, see also: [Libraries - Lock file](02-libraries.md#lock-file).

---

## Updating dependencies to their latest versions

As mentioned above, the `composer.lock` file prevents you from automatically getting
the latest versions of your dependencies. To update to the latest versions, use the
[`update`](03-cli.md#update-u) command. This will fetch the latest matching
versions (according to your `composer.json` file) and update the lock file
with the new versions. (This is equivalent to deleting the `composer.lock` file
and running `install` again.)

```sh
php composer.phar update
```

> **Note:** Composer will display a Warning when executing an `install` command
> if the `composer.lock` has not been updated since changes were made to the
> `composer.json` that might affect dependency resolution.

If you only want to install, upgrade or remove one dependency, you can explicitly list it as an argument:

```sh
php composer.phar update monolog/monolog [...]
```

---

## Packagist

[Packagist](https://packagist.org/) is the main Composer repository. A Composer
repository is basically a package source: a place where you can get packages
from. Packagist aims to be the central repository that everybody uses. This
means that you can automatically `require` any package that is available there,
without further specifying where Composer should look for the package.

If you go to the [Packagist website](https://packagist.org/) (packagist.org),
you can browse and search for packages.

Any open source project using Composer is recommended to publish their packages
on Packagist. A library does not need to be on Packagist to be used by Composer,
but it enables discovery and adoption by other developers more quickly.

---

## Platform packages

Composer has platform packages, which are virtual packages for things that are
installed on the system but are not actually installable by Composer. This
includes PHP itself, PHP extensions and some system libraries.

* `php` represents the PHP version of the user, allowing you to apply
  constraints, e.g. `^7.1`. To require a 64bit version of php, you can
  require the `php-64bit` package.

* `hhvm` represents the version of the HHVM runtime and allows you to apply
  a constraint, e.g., `^2.3`.

* `ext-<name>` allows you to require PHP extensions (includes core
  extensions). Versioning can be quite inconsistent here, so it's often
  a good idea to set the constraint to `*`.  An example of an extension
  package name is `ext-gd`.

* `lib-<name>` allows constraints to be made on versions of libraries used by
  PHP. The following are available: `curl`, `iconv`, `icu`, `libxml`,
  `openssl`, `pcre`, `uuid`, `xsl`.

You can use [`show --platform`](03-cli.md#show) to get a list of your locally
available platform packages.

---

## Autoloading

For libraries that specify autoload information, Composer generates a
`vendor/autoload.php` file. You can include this file and start
using the classes that those libraries provide without any extra work:

```php
require __DIR__ . '/vendor/autoload.php';

$log = new Monolog\Logger('name');
$log->pushHandler(new Monolog\Handler\StreamHandler('app.log', Monolog\Logger::WARNING));
$log->addWarning('Foo');
```

You can even add your own code to the autoloader by adding an
[`autoload`](04-schema.md#autoload) field to `composer.json`.

```json
{
    "autoload": {
        "psr-4": {"Acme\\": "src/"}
    }
}
```

Composer will register a [PSR-4](https://www.php-fig.org/psr/psr-4/) autoloader
for the `Acme` namespace.

You define a mapping from namespaces to directories. The `src` directory would
be in your project root, on the same level as `vendor` directory is. An example
filename would be `src/Foo.php` containing an `Acme\Foo` class.

After adding the [`autoload`](04-schema.md#autoload) field, you have to re-run
this command:

```sh
php composer.phar dump-autoload
```

This command will re-generate the `vendor/autoload.php` file.
See the [`dump-autoload`](03-cli.md#dump-autoload-dumpautoload-) section for
more information.

Including that file will also return the autoloader instance, so you can store
the return value of the include call in a variable and add more namespaces.
This can be useful for autoloading classes in a test suite, for example.

```php
$loader = require __DIR__ . '/vendor/autoload.php';
$loader->addPsr4('Acme\\Test\\', __DIR__);
```

In addition to PSR-4 autoloading, Composer also supports PSR-0, classmap and
files autoloading. See the [`autoload`](04-schema.md#autoload) reference for
more information.

See also the docs on [optimizing the autoloader](articles/autoloader-optimization.md).

> **Note:** Composer provides its own autoloader. If you don't want to use that
> one, you can include `vendor/composer/autoload_*.php` files, which return
> associative arrays allowing you to configure your own autoloader.

&larr; [Intro](00-intro.md)  |  [Libraries](02-libraries.

---

# Libraries

This chapter will tell you how to make your library installable through
Composer.

## Every project is a package

As soon as you have a `composer.json` in a directory, that directory is a
package. When you add a [`require`](04-schema.md#require) to a project, you are
making a package that depends on other packages. The only difference between
your project and a library is that your project is a package without a name.

In order to make that package installable you need to give it a name. You do
this by adding the [`name`](04-schema.md#name) property in `composer.json`:

```json
{
    "name": "acme/hello-world",
    "require": {
        "monolog/monolog": "1.0.*"
    }
}
```

In this case the project name is `acme/hello-world`, where `acme` is the vendor
name. Supplying a vendor name is mandatory.

> **Note:** If you don't know what to use as a vendor name, your GitHub
> username is usually a good bet. While package names are case-insensitive, the
> convention is all lowercase and dashes for word separation.

---

## Library Versioning

In the vast majority of cases, you will be maintaining your library using some
sort of version control system like git, svn, hg or fossil. In these cases,
Composer infers versions from your VCS, and you **should not** specify a version
in your `composer.json` file. (See the [Versions article](articles/versions.md)
to learn about how Composer uses VCS branches and tags to resolve version
constraints.)

If you are maintaining packages by hand (i.e., without a VCS), you'll need to
specify the version explicitly by adding a `version` value in your `composer.json`
file:

```json
{
    "version": "1.0.0"
}
```

> **Note:** When you add a hardcoded version to a VCS, the version will conflict
> with tag names. Composer will not be able to determine the version number.

### VCS Versioning

Composer uses your VCS's branch and tag features to resolve the version
constraints you specify in your [`require`](04-schema.md#require) field to specific sets of files.
When determining valid available versions, Composer looks at all of your tags
and branches and translates their names into an internal list of options that
it then matches against the version constraint you provided.

For more on how Composer treats tags and branches and how it resolves package
version constraints, read the [versions](articles/versions.md) article.

---

## Lock file

For your library you may commit the `composer.lock` file if you want to. This
can help your team to always test against the same dependency versions.
However, this lock file will not have any effect on other projects that depend
on it. It only has an effect on the main project.

If you do not want to commit the lock file, and you are using git, add it to
the `.gitignore`.

---

## Publishing to a VCS

Once you have a VCS repository (version control system, e.g. git) containing a
`composer.json` file, your library is already composer-installable. In this
example we will publish the `acme/hello-world` library on GitHub under
`github.com/username/hello-world`.

Now, to test installing the `acme/hello-world` package, we create a new
project locally. We will call it `acme/blog`. This blog will depend on
`acme/hello-world`, which in turn depends on `monolog/monolog`. We can
accomplish this by creating a new `blog` directory somewhere, containing a
`composer.json`:

```json
{
    "name": "acme/blog",
    "require": {
        "acme/hello-world": "dev-master"
    }
}
```

The name is not needed in this case, since we don't want to publish the blog
as a library. It is added here to clarify which `composer.json` is being
described.

Now we need to tell the blog app where to find the `hello-world` dependency.
We do this by adding a package repository specification to the blog's
`composer.json`:

```json
{
    "name": "acme/blog",
    "repositories": [
        {
            "type": "vcs",
            "url": "https://github.com/username/hello-world"
        }
    ],
    "require": {
        "acme/hello-world": "dev-master"
    }
}
```

For more details on how package repositories work and what other types are
available, see [Repositories](05-repositories.md).

That's all. You can now install the dependencies by running Composer's
[`install`](03-cli.md#install) command!

**Recap:** Any git/svn/hg/fossil repository containing a `composer.json` can be
added to your project by specifying the package repository and declaring the
dependency in the [`require`](04-schema.md#require) field.

---

## Publishing to packagist

Alright, so now you can publish packages. But specifying the VCS repository
every time is cumbersome. You don't want to force all your users to do that.

The other thing that you may have noticed is that we did not specify a package
repository for `monolog/monolog`. How did that work? The answer is Packagist.

[Packagist](https://packagist.org/) is the main package repository for
Composer, and it is enabled by default. Anything that is published on
Packagist is available automatically through Composer. Since
[Monolog is on Packagist](https://packagist.org/packages/monolog/monolog), we
can depend on it without having to specify any additional repositories.

If we wanted to share `hello-world` with the world, we would publish it on
Packagist as well.

You visit [Packagist](https://packagist.org) and hit the "Submit"
button. This will prompt you to sign up if you haven't already, and then
allows you to submit the URL to your VCS repository, at which point Packagist
will start crawling it. Once it is done, your package will be available to
anyone!

&larr; [Basic usage](01-basic-usage.md) |  [Command-line interface](03-cli.md) &rarr;

---

# Command-line interface / Commands

You've already learned how to use the command-line interface to do some
things. This chapter documents all the available commands.

To get help from the command-line, call `composer` or `composer list`
to see the complete list of commands, then `--help` combined with any of those
can give you more information.

As Composer uses [symfony/console](https://github.com/symfony/console) you can call commands by short name if it's not ambiguous.
```sh
composer dump
```
calls `composer dump-autoload`.

---

## Global Options

The following options are available with every command:

* **--verbose (-v):** Increase verbosity of messages.
* **--help (-h):** Display help information.
* **--quiet (-q):** Do not output any message.
* **--no-interaction (-n):** Do not ask any interactive question.
* **--no-plugins:** Disables plugins.
* **--no-cache:** Disables the use of the cache directory. Same as setting the COMPOSER_CACHE_DIR
  env var to /dev/null (or NUL on Windows).
* **--working-dir (-d):** If specified, use the given directory as working directory.
* **--profile:** Display timing and memory usage information
* **--ansi:** Force ANSI output.
* **--no-ansi:** Disable ANSI output.
* **--version (-V):** Display this application version.

---

## Process Exit Codes

* **0:** OK
* **1:** Generic/unknown error code
* **2:** Dependency solving error code

---

## init

In the [Libraries](02-libraries.md) chapter we looked at how to create a
`composer.json` by hand. There is also an `init` command available to do this.

When you run the command it will interactively ask you to fill in the fields,
while using some smart defaults.

```sh
php composer.phar init
```

### Options

* **--name:** Name of the package.
* **--description:** Description of the package.
* **--author:** Author name of the package.
* **--type:** Type of package.
* **--homepage:** Homepage of the package.
* **--require:** Package to require with a version constraint. Should be
  in format `foo/bar:1.0.0`.
* **--require-dev:** Development requirements, see **--require**.
* **--stability (-s):** Value for the `minimum-stability` field.
* **--license (-l):** License of package.
* **--repository:** Provide one (or more) custom repositories. They will be stored
  in the generated composer.json, and used for auto-completion when prompting for
  the list of requires. Every repository can be either an HTTP URL pointing
  to a `composer` repository or a JSON string which similar to what the
  [repositories](04-schema.md#repositories) key accepts.

---

## install / i

The `install` command reads the `composer.json` file from the current
directory, resolves the dependencies, and installs them into `vendor`.

```sh
php composer.phar install
```

If there is a `composer.lock` file in the current directory, it will use the
exact versions from there instead of resolving them. This ensures that
everyone using the library will get the same versions of the dependencies.

If there is no `composer.lock` file, Composer will create one after dependency
resolution.

### Options

* **--prefer-source:** There are two ways of downloading a package: `source`
  and `dist`. For stable versions Composer will use the `dist` by default.
  The `source` is a version control repository. If `--prefer-source` is
  enabled, Composer will install from `source` if there is one. This is
  useful if you want to make a bugfix to a project and get a local git
  clone of the dependency directly.
* **--prefer-dist:** Reverse of `--prefer-source`, Composer will install
  from `dist` if possible. This can speed up installs substantially on build
  servers and other use cases where you typically do not run updates of the
  vendors. It is also a way to circumvent problems with git if you do not
  have a proper setup.
* **--dry-run:** If you want to run through an installation without actually
  installing a package, you can use `--dry-run`. This will simulate the
  installation and show you what would happen.
* **--dev:** Install packages listed in `require-dev` (this is the default behavior).
* **--no-dev:** Skip installing packages listed in `require-dev`. The autoloader
  generation skips the `autoload-dev` rules.
* **--no-autoloader:** Skips autoloader generation.
* **--no-scripts:** Skips execution of scripts defined in `composer.json`.
* **--no-progress:** Removes the progress display that can mess with some
  terminals or scripts which don't handle backspace characters.
* **--optimize-autoloader (-o):** Convert PSR-0/4 autoloading to classmap to get a faster
  autoloader. This is recommended especially for production, but can take
  a bit of time to run so it is currently not done by default.
* **--classmap-authoritative (-a):** Autoload classes from the classmap only.
  Implicitly enables `--optimize-autoloader`.
* **--apcu-autoloader:** Use APCu to cache found/not-found classes.
* **--apcu-autoloader-prefix:** Use a custom prefix for the APCu autoloader cache.
  Implicitly enables `--apcu-autoloader`.
* **--ignore-platform-reqs:** ignore all platform requirements (`php`, `hhvm`,
  `lib-*` and `ext-*`) and force the installation even if the local machine does
  not fulfill these.
  See also the [`platform`](06-config.md#platform) config option.
* **--ignore-platform-req:** ignore a specific platform requirement(`php`,
  `hhvm`, `lib-*` and `ext-*`) and force the installation even if the local machine
  does not fulfill it.

---

## update / u

In order to get the latest versions of the dependencies and to update the
`composer.lock` file, you should use the `update` command. This command is also
aliased as `upgrade` as it does the same as `upgrade` does if you are thinking
of `apt-get` or similar package managers.

```sh
php composer.phar update
```

This will resolve all dependencies of the project and write the exact versions
into `composer.lock`.

If you only want to update a few packages and not all, you can list them as such:

```sh
php composer.phar update vendor/package vendor/package2
```

You can also use wildcards to update a bunch of packages at once:

```sh
php composer.phar update "vendor/*"
```


If you want to downgrade a package to a specific version without changing your
composer.json you can use `--with` and provide a custom version constraint:

```sh
php composer.phar update --with vendor/package:2.0.1
```

The custom constraint has to be a subset of the existing constraint you have,
and this feature is only available for your root package dependencies.

If you only want to update the package(s) for which you provide custom constraints
using `--with`, you can skip `--with` and just use constraints with the partial
update syntax:

```sh
php composer.phar update vendor/package:2.0.1 vendor/package2:3.0.*
```


### Options

* **--prefer-source:** Install packages from `source` when available.
* **--prefer-dist:** Install packages from `dist` when available.
* **--dry-run:** Simulate the command without actually doing anything.
* **--dev:** Install packages listed in `require-dev` (this is the default behavior).
* **--no-dev:** Skip installing packages listed in `require-dev`. The autoloader generation skips the `autoload-dev` rules.
* **--no-install:** Does not run the install step after updating the composer.lock file.
* **--lock:** Only updates the lock file hash to suppress warning about the
  lock file being out of date.
* **--with:** Temporary version constraint to add, e.g. foo/bar:1.0.0 or foo/bar=1.0.0
* **--no-autoloader:** Skips autoloader generation.
* **--no-scripts:** Skips execution of scripts defined in `composer.json`.
* **--no-progress:** Removes the progress display that can mess with some
  terminals or scripts which don't handle backspace characters.
* **--with-dependencies (-w):** Update also dependencies of packages in the argument list, except those which are root requirements.
* **--with-all-dependencies (-W):** Update also dependencies of packages in the argument list, including those which are root requirements.
* **--optimize-autoloader (-o):** Convert PSR-0/4 autoloading to classmap to get a faster
  autoloader. This is recommended especially for production, but can take
  a bit of time to run, so it is currently not done by default.
* **--classmap-authoritative (-a):** Autoload classes from the classmap only.
  Implicitly enables `--optimize-autoloader`.
* **--apcu-autoloader:** Use APCu to cache found/not-found classes.
* **--apcu-autoloader-prefix:** Use a custom prefix for the APCu autoloader cache.
  Implicitly enables `--apcu-autoloader`.
* **--ignore-platform-reqs:** ignore all platform requirements (`php`, `hhvm`,
  `lib-*` and `ext-*`) and force the installation even if the local machine does
  not fulfill these.
  See also the [`platform`](06-config.md#platform) config option.
* **--ignore-platform-req:** ignore a specific platform requirement(`php`,
  `hhvm`, `lib-*` and `ext-*`) and force the installation even if the local machine
  does not fulfill it.
* **--prefer-stable:** Prefer stable versions of dependencies.
* **--prefer-lowest:** Prefer lowest versions of dependencies. Useful for testing minimal
  versions of requirements, generally used with `--prefer-stable`.
* **--interactive:** Interactive interface with autocompletion to select the packages to update.
* **--root-reqs:** Restricts the update to your first degree dependencies.

Specifying one of the words `mirrors`, `lock`, or `nothing` as an argument has the same effect as specifying the option `--lock`, for example `composer update mirrors` is exactly the same as `composer update --lock`.

---

## require

The `require` command adds new packages to the `composer.json` file from
the current directory. If no file exists one will be created on the fly.

```sh
php composer.phar require
```

After adding/changing the requirements, the modified requirements will be
installed or updated.

If you do not want to choose requirements interactively, you can pass them
to the command.

```sh
php composer.phar require "vendor/package:2.*" vendor/package2:dev-master
```

If you do not specify a package, composer will prompt you to search for a package, and given results, provide a list of  matches to require.

### Options

* **--dev:** Add packages to `require-dev`.
* **--dry-run:** Simulate the command without actually doing anything.
* **--prefer-source:** Install packages from `source` when available.
* **--prefer-dist:** Install packages from `dist` when available.
* **--no-progress:** Removes the progress display that can mess with some
  terminals or scripts which don't handle backspace characters.
* **--no-update:** Disables the automatic update of the dependencies (implies --no-install).
* **--no-install:** Does not run the install step after updating the composer.lock file.
* **--no-scripts:** Skips execution of scripts defined in `composer.json`.
* **--update-no-dev:** Run the dependency update with the `--no-dev` option.
* **--update-with-dependencies (-w):** Also update dependencies of the newly required packages, except those that are root requirements.
* **--update-with-all-dependencies (-W):** Also update dependencies of the newly required packages, including those that are root requirements.
* **--ignore-platform-reqs:** ignore all platform requirements (`php`, `hhvm`,
  `lib-*` and `ext-*`) and force the installation even if the local machine does
  not fulfill these.
  See also the [`platform`](06-config.md#platform) config option.
* **--ignore-platform-req:** ignore a specific platform requirement(`php`,
  `hhvm`, `lib-*` and `ext-*`) and force the installation even if the local machine
  does not fulfill it.
* **--prefer-stable:** Prefer stable versions of dependencies.
* **--prefer-lowest:** Prefer lowest versions of dependencies. Useful for testing minimal
  versions of requirements, generally used with `--prefer-stable`.
* **--sort-packages:** Keep packages sorted in `composer.json`.
* **--optimize-autoloader (-o):** Convert PSR-0/4 autoloading to classmap to
  get a faster autoloader. This is recommended especially for production, but
  can take a bit of time to run, so it is currently not done by default.
* **--classmap-authoritative (-a):** Autoload classes from the classmap only.
  Implicitly enables `--optimize-autoloader`.
* **--apcu-autoloader:** Use APCu to cache found/not-found classes.
* **--apcu-autoloader-prefix:** Use a custom prefix for the APCu autoloader cache.
  Implicitly enables `--apcu-autoloader`.

---

## remove

The `remove` command removes packages from the `composer.json` file from
the current directory.

```sh
php composer.phar remove vendor/package vendor/package2
```

After removing the requirements, the modified requirements will be
uninstalled.

### Options
* **--dev:** Remove packages from `require-dev`.
* **--dry-run:** Simulate the command without actually doing anything.
* **--no-progress:** Removes the progress display that can mess with some
  terminals or scripts which don't handle backspace characters.
* **--no-update:** Disables the automatic update of the dependencies (implies --no-install).
* **--no-install:** Does not run the install step after updating the composer.lock file.
* **--no-scripts:** Skips execution of scripts defined in `composer.json`.
* **--update-no-dev:** Run the dependency update with the --no-dev option.
* **--update-with-dependencies (-w):** Also update dependencies of the removed packages.
  (Deprecrated, is now default behavior)
* **--update-with-all-dependencies (-W):** Allows all inherited dependencies to be updated,
  including those that are root requirements.
* **--ignore-platform-reqs:** ignore all platform requirements (`php`, `hhvm`,
  `lib-*` and `ext-*`) and force the installation even if the local machine does
  not fulfill these.
  See also the [`platform`](06-config.md#platform) config option.
* **--ignore-platform-req:** ignore a specific platform requirement(`php`,
  `hhvm`, `lib-*` and `ext-*`) and force the installation even if the local machine
  does not fulfill it.
* **--optimize-autoloader (-o):** Convert PSR-0/4 autoloading to classmap to
  get a faster autoloader. This is recommended especially for production, but
  can take a bit of time to run so it is currently not done by default.
* **--classmap-authoritative (-a):** Autoload classes from the classmap only.
  Implicitly enables `--optimize-autoloader`.
* **--apcu-autoloader:** Use APCu to cache found/not-found classes.
* **--apcu-autoloader-prefix:** Use a custom prefix for the APCu autoloader cache.
  Implicitly enables `--apcu-autoloader`.

---

## check-platform-reqs

The check-platform-reqs command checks that your PHP and extensions versions
match the platform requirements of the installed packages. This can be used
to verify that a production server has all the extensions needed to run a
project after installing it for example.

Unlike update/install, this command will ignore config.platform settings and
check the real platform packages so you can be certain you have the required
platform dependencies.

---

## global

The global command allows you to run other commands like `install`, `remove`, `require`
or `update` as if you were running them from the [COMPOSER_HOME](#composer-home)
directory.

This is merely a helper to manage a project stored in a central location that
can hold CLI tools or Composer plugins that you want to have available everywhere.

This can be used to install CLI utilities globally. Here is an example:

```sh
php composer.phar global require friendsofphp/php-cs-fixer
```

Now the `php-cs-fixer` binary is available globally. Make sure your global
[vendor binaries](articles/vendor-binaries.md) directory is in your `$PATH`
environment variable, you can get its location with the following command :

```sh
php composer.phar global config bin-dir --absolute
```

If you wish to update the binary later on you can run a global update:

```sh
php composer.phar global update
```

---

## search

The search command allows you to search through the current project's package
repositories. Usually this will be packagist. You pass it the terms you want
to search for.

```sh
php composer.phar search monolog
```

You can also search for more than one term by passing multiple arguments.

### Options

* **--only-name (-N):** Search only in name.
* **--type (-t):** Search for a specific package type.

---

## show

To list all of the available packages, you can use the `show` command.

```sh
php composer.phar show
```

To filter the list you can pass a package mask using wildcards.

```sh
php composer.phar show monolog/*

monolog/monolog 1.19.0 Sends your logs to files, sockets, inboxes, databases and various web services
```

If you want to see the details of a certain package, you can pass the package
name.

```sh
php composer.phar show monolog/monolog

name     : monolog/monolog
versions : master-dev, 1.0.2, 1.0.1, 1.0.0, 1.0.0-RC1
type     : library
names    : monolog/monolog
source   : [git] https://github.com/Seldaek/monolog.git 3d4e60d0cbc4b888fe5ad223d77964428b1978da
dist     : [zip] https://github.com/Seldaek/monolog/zipball/3d4e60d0cbc4b888fe5ad223d77964428b1978da 3d4e60d0cbc4b888fe5ad223d77964428b1978da
license  : MIT

autoload
psr-0
Monolog : src/

requires
php >=5.3.0
```

You can even pass the package version, which will tell you the details of that
specific version.

```sh
php composer.phar show monolog/monolog 1.0.2
```

### Options

* **--all :** List all packages available in all your repositories.
* **--installed (-i):** List the packages that are installed (this is enabled by default, and deprecated).
* **--locked:** List the locked packages from composer.lock.
* **--platform (-p):** List only platform packages (php & extensions).
* **--available (-a):** List available packages only.
* **--self (-s):** List the root package info.
* **--name-only (-N):** List package names only.
* **--path (-P):** List package paths.
* **--tree (-t):** List your dependencies as a tree. If you pass a package name it will show the dependency tree for that package.
* **--latest (-l):** List all installed packages including their latest version.
* **--outdated (-o):** Implies --latest, but this lists *only* packages that have a newer version available.
* **--no-dev:** Filters dev dependencies from the package list.
* **--minor-only (-m):** Use with --latest. Only shows packages that have minor SemVer-compatible updates.
* **--direct (-D):** Restricts the list of packages to your direct dependencies.
* **--strict:** Return a non-zero exit code when there are outdated packages.
* **--format (-f):** Lets you pick between text (default) or json output format.

---

## outdated

The `outdated` command shows a list of installed packages that have updates available,
including their current and latest versions. This is basically an alias for
`composer show -lo`.

The color coding is as such:

- **green (=)**: Dependency is in the latest version and is up to date.
- **yellow (`~`)**: Dependency has a new version available that includes backwards compatibility breaks according to semver, so upgrade when
  you can but it may involve work.
- **red (!)**: Dependency has a new version that is semver-compatible and you should upgrade it.

### Options

* **--all (-a):** Show all packages, not just outdated (alias for `composer show -l`).
* **--direct (-D):** Restricts the list of packages to your direct dependencies.
* **--strict:** Returns non-zero exit code if any package is outdated.
* **--minor-only (-m):** Only shows packages that have minor SemVer-compatible updates.
* **--format (-f):** Lets you pick between text (default) or json output format.
* **--no-dev:** Do not show outdated dev dependencies.
* **--locked:** Shows updates for packages from the lock file, regardless of what is currently in vendor dir.

---

## browse / home

The `browse` (aliased to `home`) opens a package's repository URL or homepage
in your browser.

### Options

* **--homepage (-H):** Open the homepage instead of the repository URL.
* **--show (-s):** Only show the homepage or repository URL.

---

## suggests

Lists all packages suggested by currently installed set of packages. You can
optionally pass one or multiple package names in the format of `vendor/package`
to limit output to suggestions made by those packages only.

Use the `--by-package` (default) or `--by-suggestion` flags to group the output by
the package offering the suggestions or the suggested packages respectively.

If you only want a list of suggested package names, use `--list`.

### Options

* **--by-package:** Groups output by suggesting package (default).
* **--by-suggestion:** Groups output by suggested package.
* **--all:** Show suggestions from all dependencies, including transitive ones (by
  default only direct dependencies' suggestions are shown).
* **--list:** Show only list of suggested package names.
* **--no-dev:** Excludes suggestions from `require-dev` packages.

---

## fund

Discover how to help fund the maintenance of your dependencies. This lists
all funding links from the installed dependencies.

---

## depends (why)

The `depends` command tells you which other packages depend on a certain
package. As with installation `require-dev` relationships are only considered
for the root package.

```sh
php composer.phar depends doctrine/lexer
 doctrine/annotations v1.2.7 requires doctrine/lexer (1.*)
 doctrine/common      v2.6.1 requires doctrine/lexer (1.*)
```

You can optionally specify a version constraint after the package to limit the
search.

Add the `--tree` or `-t` flag to show a recursive tree of why the package is
depended upon, for example:

```sh
php composer.phar depends psr/log -t
psr/log 1.0.0 Common interface for logging libraries
|- aboutyou/app-sdk 2.6.11 (requires psr/log 1.0.*)
|  `- __root__ (requires aboutyou/app-sdk ^2.6)
|- monolog/monolog 1.17.2 (requires psr/log ~1.0)
|  `- laravel/framework v5.2.16 (requires monolog/monolog ~1.11)
|     `- __root__ (requires laravel/framework ^5.2)
`- symfony/symfony v3.0.2 (requires psr/log ~1.0)
   `- __root__ (requires symfony/symfony ^3.0)
```

### Options

* **--recursive (-r):** Recursively resolves up to the root package.
* **--tree (-t):** Prints the results as a nested tree, implies -r.

---

## prohibits (why-not)

The `prohibits` command tells you which packages are blocking a given package
from being installed. Specify a version constraint to verify whether upgrades
can be performed in your project, and if not why not. See the following
example:

```sh
php composer.phar prohibits symfony/symfony 3.1
 laravel/framework v5.2.16 requires symfony/var-dumper (2.8.*|3.0.*)
```

Note that you can also specify platform requirements, for example to check
whether you can upgrade your server to PHP 8.0:

```sh
php composer.phar prohibits php:8
 doctrine/cache        v1.6.0 requires php (~5.5|~7.0)
 doctrine/common       v2.6.1 requires php (~5.5|~7.0)
 doctrine/instantiator 1.0.5  requires php (>=5.3,<8.0-DEV)
```

As with `depends` you can request a recursive lookup, which will list all
packages depending on the packages that cause the conflict.

### Options

* **--recursive (-r):** Recursively resolves up to the root package.
* **--tree (-t):** Prints the results as a nested tree, implies -r.

---

## validate

You should always run the `validate` command before you commit your
`composer.json` file, and before you tag a release. It will check if your
`composer.json` is valid.

```sh
php composer.phar validate
```

### Options

* **--no-check-all:** Do not emit a warning if requirements in `composer.json` use unbound or overly strict version constraints.
* **--no-check-lock:** Do not emit an error if `composer.lock` exists and is not up to date.
* **--no-check-publish:** Do not emit an error if `composer.json` is unsuitable for publishing as a package on Packagist but is otherwise valid.
* **--with-dependencies:** Also validate the composer.json of all installed dependencies.
* **--strict:** Return a non-zero exit code for warnings as well as errors.

---

## status

If you often need to modify the code of your dependencies and they are
installed from source, the `status` command allows you to check if you have
local changes in any of them.

```sh
php composer.phar status
```

With the `--verbose` option you get some more information about what was
changed:

```sh
php composer.phar status -v

You have changes in the following dependencies:
vendor/seld/jsonlint:
    M README.mdown
```

---

## self-update (selfupdate)

To update Composer itself to the latest version, run the `self-update`
command. It will replace your `composer.phar` with the latest version.

```sh
php composer.phar self-update
```

If you would like to instead update to a specific release specify it:

```sh
php composer.phar self-update 1.0.0-alpha7
```

If you have installed Composer for your entire system (see [global installation](00-intro.md#globally)),
you may have to run the command with `root` privileges

```sh
sudo -H composer self-update
```

If Composer was not installed as a PHAR, this command is not available.
(This is sometimes the case when Composer was installed by an operating system package manager.)

### Options

* **--rollback (-r):** Rollback to the last version you had installed.
* **--clean-backups:** Delete old backups during an update. This makes the
  current version of Composer the only backup available after the update.
* **--no-progress:** Do not output download progress.
* **--update-keys:** Prompt user for a key update.
* **--stable:** Force an update to the stable channel.
* **--preview:** Force an update to the preview channel.
* **--snapshot:** Force an update to the snapshot channel.
* **--1:** Force an update to the stable channel, but only use 1.x versions
* **--2:** Force an update to the stable channel, but only use 2.x versions
* **--set-channel-only:** Only store the channel as the default one and then exit

---

## config

The `config` command allows you to edit composer config settings and repositories
in either the local `composer.json` file or the global `config.json` file.

Additionally it lets you edit most properties in the local `composer.json`.

```sh
php composer.phar config --list
```

### Usage

`config [options] [setting-key] [setting-value1] ... [setting-valueN]`

`setting-key` is a configuration option name and `setting-value1` is a
configuration value.  For settings that can take an array of values (like
`github-protocols`), more than one setting-value arguments are allowed.

You can also edit the values of the following properties:

`description`, `homepage`, `keywords`, `license`, `minimum-stability`,
`name`, `prefer-stable`, `type` and `version`.

See the [Config](06-config.md) chapter for valid configuration options.

### Options

* **--global (-g):** Operate on the global config file located at
  `$COMPOSER_HOME/config.json` by default.  Without this option, this command
  affects the local composer.json file or a file specified by `--file`.
* **--editor (-e):** Open the local composer.json file using in a text editor as
  defined by the `EDITOR` env variable.  With the `--global` option, this opens
  the global config file.
* **--auth (-a):** Affect auth config file (only used for --editor).
* **--unset:** Remove the configuration element named by `setting-key`.
* **--list (-l):** Show the list of current config variables.  With the `--global`
  option this lists the global configuration only.
* **--file="..." (-f):** Operate on a specific file instead of composer.json. Note
  that this cannot be used in conjunction with the `--global` option.
* **--absolute:** Returns absolute paths when fetching `*-dir` config values
  instead of relative.
* **--json:** JSON decode the setting value, to be used with `extra.*` keys.
* **--merge:** Merge the setting value with the current value, to be used with `extra.*` keys in combination with `--json`.

### Modifying Repositories

In addition to modifying the config section, the `config` command also supports making
changes to the repositories section by using it the following way:

```sh
php composer.phar config repositories.foo vcs https://github.com/foo/bar
```

If your repository requires more configuration options, you can instead pass its JSON representation :

```sh
php composer.phar config repositories.foo '{"type": "vcs", "url": "http://svn.example.org/my-project/", "trunk-path": "master"}'
```

### Modifying Extra Values

In addition to modifying the config section, the `config` command also supports making
changes to the extra section by using it the following way:

```sh
php composer.phar config extra.foo.bar value
```

The dots indicate array nesting, a max depth of 3 levels is allowed though. The above
would set `"extra": { "foo": { "bar": "value" } }`.

If you have a complex value to add/modify, you can use the `--json` and `--merge` flags
to edit extra fields as json:

```sh
php composer.phar config --json extra.foo.bar '{"baz": true, "qux": []}'
```

---

## create-project

You can use Composer to create new projects from an existing package. This is
the equivalent of doing a git clone/svn checkout followed by a `composer install`
of the vendors.

There are several applications for this:

1. You can deploy application packages.
2. You can check out any package and start developing on patches for example.
3. Projects with multiple developers can use this feature to bootstrap the
   initial application for development.

To create a new project using Composer you can use the `create-project` command.
Pass it a package name, and the directory to create the project in. You can also
provide a version as third argument, otherwise the latest version is used.

If the directory does not currently exist, it will be created during installation.

```sh
php composer.phar create-project doctrine/orm path "2.2.*"
```

It is also possible to run the command without params in a directory with an
existing `composer.json` file to bootstrap a project.

By default the command checks for the packages on packagist.org.

### Options

* **--stability (-s):** Minimum stability of package. Defaults to `stable`.
* **--prefer-source:** Install packages from `source` when available.
* **--prefer-dist:** Install packages from `dist` when available.
* **--repository:** Provide a custom repository to search for the package,
  which will be used instead of packagist. Can be either an HTTP URL pointing
  to a `composer` repository, a path to a local `packages.json` file, or a
  JSON string which similar to what the [repositories](04-schema.md#repositories)
  key accepts. You can use this multiple times to configure multiple repositories.
* **--add-repository:** Add the custom repository in the composer.json. If a lock
  file is present it will be deleted and an update will be run instead of install.
* **--dev:** Install packages listed in `require-dev`.
* **--no-dev:** Disables installation of require-dev packages.
* **--no-scripts:** Disables the execution of the scripts defined in the root
  package.
* **--no-progress:** Removes the progress display that can mess with some
  terminals or scripts which don't handle backspace characters.
* **--no-secure-http:** Disable the secure-http config option temporarily while
  installing the root package. Use at your own risk. Using this flag is a bad
  idea.
* **--keep-vcs:** Skip the deletion of the VCS metadata for the created
  project. This is mostly useful if you run the command in non-interactive
  mode.
* **--remove-vcs:** Force-remove the VCS metadata without prompting.
* **--no-install:** Disables installation of the vendors.
* **--ignore-platform-reqs:** ignore all platform requirements (`php`, `hhvm`,
  `lib-*` and `ext-*`) and force the installation even if the local machine does
  not fulfill these.
  See also the [`platform`](06-config.md#platform) config option.
* **--ignore-platform-req:** ignore a specific platform requirement(`php`,
  `hhvm`, `lib-*` and `ext-*`) and force the installation even if the local machine
  does not fulfill it.
* **--ask:** Ask user to provide target directory for new project.

---

## dump-autoload (dumpautoload)

If you need to update the autoloader because of new classes in a classmap
package for example, you can use `dump-autoload` to do that without having to
go through an install or update.

Additionally, it can dump an optimized autoloader that converts PSR-0/4 packages
into classmap ones for performance reasons. In large applications with many
classes, the autoloader can take up a substantial portion of every request's
time. Using classmaps for everything is less convenient in development, but
using this option you can still use PSR-0/4 for convenience and classmaps for
performance.

### Options
* **--no-scripts:** Skips the execution of all scripts defined in `composer.json` file.
* **--optimize (-o):** Convert PSR-0/4 autoloading to classmap to get a faster
  autoloader. This is recommended especially for production, but can take
  a bit of time to run, so it is currently not done by default.
* **--classmap-authoritative (-a):** Autoload classes from the classmap only.
  Implicitly enables `--optimize`.
* **--apcu:** Use APCu to cache found/not-found classes.
* **--apcu-prefix:** Use a custom prefix for the APCu autoloader cache.
  Implicitly enables `--apcu`.
* **--no-dev:** Disables autoload-dev rules.
* **--ignore-platform-reqs:** ignore all `php`, `hhvm`, `lib-*` and `ext-*`
  requirements and skip the [platform check](07-runtime.md#platform-check) for these.
  See also the [`platform`](06-config.md#platform) config option.
* **--ignore-platform-req:** ignore a specific platform requirement (`php`, `hhvm`,
  `lib-*` and `ext-*`) and skip the [platform check](07-runtime.md#platform-check) for it.

---

## clear-cache / clearcache / cc

Deletes all content from Composer's cache directories.

---

## licenses

Lists the name, version and license of every package installed. Use
`--format=json` to get machine-readable output.

### Options

* **--format:** Format of the output: text, json or summary (default: "text")
* **--no-dev:** Remove dev dependencies from the output

---

## run-script

### Options

* **--timeout:** Set the script timeout in seconds, or 0 for no timeout.
* **--dev:** Sets the dev mode.
* **--no-dev:** Disable dev mode.
* **--list (-l):** List user defined scripts.

To run [scripts](articles/scripts.md) manually you can use this command,
give it the script name and optionally any required arguments.

---

## exec

Executes a vendored binary/script. You can execute any command and this will
ensure that the Composer bin-dir is pushed on your PATH before the command
runs.

### Options

* **--list (-l):** List the available composer binaries.

---

## diagnose

If you think you found a bug, or something is behaving strangely, you might
want to run the `diagnose` command to perform automated checks for many common
problems.

```sh
php composer.phar diagnose
```

---

## archive

This command is used to generate a zip/tar archive for a given package in a
given version. It can also be used to archive your entire project without
excluded/ignored files.

```sh
php composer.phar archive vendor/package 2.0.21 --format=zip
```

### Options

* **--format (-f):** Format of the resulting archive: tar or zip (default:
  "tar")
* **--dir:** Write the archive to this directory (default: ".")
* **--file:** Write the archive with the given file name.

---

## help

To get more information about a certain command, you can use `help`.

```sh
php composer.phar help install
```

---

## Command-line completion

Command-line completion can be enabled by following instructions
[on this page](https://github.com/bamarni/symfony-console-autocomplete).

---

## Environment variables

You can set a number of environment variables that override certain settings.
Whenever possible it is recommended to specify these settings in the `config`
section of `composer.json` instead. It is worth noting that the env vars will
always take precedence over the values specified in `composer.json`.

### COMPOSER

By setting the `COMPOSER` env variable it is possible to set the filename of
`composer.json` to something else.

For example:

```sh
COMPOSER=composer-other.json php composer.phar install
```

The generated lock file will use the same name: `composer-other.lock` in this example.

### COMPOSER_ALLOW_SUPERUSER

If set to 1, this env disables the warning about running commands as root/super user.
It also disables automatic clearing of sudo sessions, so you should really only set this
if you use Composer as super user at all times like in docker containers.

### COMPOSER_ALLOW_XDEBUG

If set to 1, this env allows running Composer when the Xdebug extension is enabled, without restarting PHP without it.

### COMPOSER_AUTH

The `COMPOSER_AUTH` var allows you to set up authentication as an environment variable.
The contents of the variable should be a JSON formatted object containing [http-basic,
github-oauth, bitbucket-oauth, ... objects as needed](articles/authentication-for-private-packages.md),
and following the
[spec from the config](06-config.md).

### COMPOSER_BIN_DIR

By setting this option you can change the `bin` ([Vendor Binaries](articles/vendor-binaries.md))
directory to something other than `vendor/bin`.

### COMPOSER_CACHE_DIR

The `COMPOSER_CACHE_DIR` var allows you to change the Composer cache directory,
which is also configurable via the [`cache-dir`](06-config.md#cache-dir) option.

By default, it points to `$COMPOSER_HOME/cache` on \*nix and macOS, and
`C:\Users\<user>\AppData\Local\Composer` (or `%LOCALAPPDATA%/Composer`) on Windows.

### COMPOSER_CAFILE

By setting this environmental value, you can set a path to a certificate bundle
file to be used during SSL/TLS peer verification.

### COMPOSER_DISABLE_XDEBUG_WARN

If set to 1, this env suppresses a warning when Composer is running with the Xdebug extension enabled.

### COMPOSER_DISCARD_CHANGES

This env var controls the [`discard-changes`](06-config.md#discard-changes) config option.

### COMPOSER_HOME

The `COMPOSER_HOME` var allows you to change the Composer home directory. This
is a hidden, global (per-user on the machine) directory that is shared between
all projects.

By default, it points to `C:\Users\<user>\AppData\Roaming\Composer` on Windows
and `/Users/<user>/.composer` on macOS. On \*nix systems that follow the [XDG Base
Directory Specifications](https://specifications.freedesktop.org/basedir-spec/basedir-spec-latest.html),
it points to `$XDG_CONFIG_HOME/composer`. On other \*nix systems, it points to
`/home/<user>/.composer`.

#### COMPOSER_HOME/config.json

You may put a `config.json` file into the location which `COMPOSER_HOME` points
to. Composer will merge this configuration with your project's `composer.json`
when you run the `install` and `update` commands.

This file allows you to set [repositories](05-repositories.md) and
[configuration](06-config.md) for the user's projects.

In case global configuration matches _local_ configuration, the _local_
configuration in the project's `composer.json` always wins.

### COMPOSER_HTACCESS_PROTECT

Defaults to `1`. If set to `0`, Composer will not create `.htaccess` files in the
composer home, cache, and data directories.

### COMPOSER_MEMORY_LIMIT

If set, the value is used as php's memory_limit.

### COMPOSER_MIRROR_PATH_REPOS

If set to 1, this env changes the default path repository strategy to `mirror` instead
of `symlink`. As it is the default strategy being set it can still be overwritten by
repository options.

### COMPOSER_NO_INTERACTION

If set to 1, this env var will make Composer behave as if you passed the
`--no-interaction` flag to every command. This can be set on build boxes/CI.

### COMPOSER_PROCESS_TIMEOUT

This env var controls the time Composer waits for commands (such as git
commands) to finish executing. The default value is 300 seconds (5 minutes).

### COMPOSER_ROOT_VERSION

By setting this var you can specify the version of the root package, if it
cannot be guessed from VCS info and is not present in `composer.json`.

### COMPOSER_VENDOR_DIR

By setting this var you can make Composer install the dependencies into a
directory other than `vendor`.

### http_proxy or HTTP_PROXY

If you are using Composer from behind an HTTP proxy, you can use the standard
`http_proxy` or `HTTP_PROXY` env vars. Set it to the URL of your proxy.
Many operating systems already set this variable for you.

Using `http_proxy` (lowercased) or even defining both might be preferable since
some tools like git or curl will only use the lower-cased `http_proxy` version.
Alternatively you can also define the git proxy using
`git config --global http.proxy <proxy url>`.

If you are using Composer in a non-CLI context (i.e. integration into a CMS or
similar use case), and need to support proxies, please provide the `CGI_HTTP_PROXY`
environment variable instead. See [httpoxy.org](https://httpoxy.org/) for further
details.

### HTTP_PROXY_REQUEST_FULLURI

If you use a proxy, but it does not support the request_fulluri flag, then you
should set this env var to `false` or `0` to prevent Composer from setting the
request_fulluri option.

### HTTPS_PROXY_REQUEST_FULLURI

If you use a proxy, but it does not support the request_fulluri flag for HTTPS
requests, then you should set this env var to `false` or `0` to prevent Composer
from setting the request_fulluri option.

### COMPOSER_SELF_UPDATE_TARGET

If set, makes the self-update command write the new Composer phar file into that path instead of overwriting itself. Useful for updating Composer on read-only filesystem.

### no_proxy or NO_PROXY

If you are behind a proxy and would like to disable it for certain domains, you
can use the `no_proxy` or `NO_PROXY` env var. Set it to a comma separated list of
domains the proxy should *not* be used for.

The env var accepts domains, IP addresses, and IP address blocks in CIDR
notation. You can restrict the filter to a particular port (e.g. `:80`). You
can also set it to `*` to ignore the proxy for all HTTP requests.

### COMPOSER_DISABLE_NETWORK

If set to `1`, disables network access (best effort). This can be used for debugging or
to run Composer on a plane or a starship with poor connectivity.

If set to `prime`, GitHub VCS repositories will prime the cache, so it can then be used
fully offline with `1`.

### COMPOSER_DEBUG_EVENTS

If set to `1`, outputs information about events being dispatched, which can be
useful for plugin authors to identify what is firing when exactly.

&larr; [Libraries](02-libraries.md)  |  [Schema](04-schema.md) &rarr;

---

# The composer.json schema

This chapter will explain all of the fields available in `composer.json`.

## JSON schema

We have a [JSON schema](https://json-schema.org) that documents the format and
can also be used to validate your `composer.json`. In fact, it is used by the
`validate` command. You can find it at: https://getcomposer.org/schema.json

---

## Root Package

The root package is the package defined by the `composer.json` at the root of
your project. It is the main `composer.json` that defines your project
requirements.

Certain fields only apply when in the root package context. One example of
this is the `config` field. Only the root package can define configuration.
The config of dependencies is ignored. This makes the `config` field
`root-only`.

> **Note:** A package can be the root package or not, depending on the context.
> For example, if your project depends on the `monolog` library, your project
> is the root package. However, if you clone `monolog` from GitHub in order to
> fix a bug in it, then `monolog` is the root package.

---

## Properties

### name

The name of the package. It consists of vendor name and project name,
separated by `/`. Examples:

* monolog/monolog
* igorw/event-source

The name must be lowercased and consist of words separated by `-`, `.` or `_`.
The complete name should match `^[a-z0-9]([_.-]?[a-z0-9]+)*/[a-z0-9](([_.]?|-{0,2})[a-z0-9]+)*$`.

The `name` property is required for published packages (libraries).

> **Note:** Before Composer version 2.0, a name could contain any character, including white spaces.

### description

A short description of the package. Usually this is one line long.

Required for published packages (libraries).

### version

The version of the package. In most cases this is not required and should
be omitted (see below).

This must follow the format of `X.Y.Z` or `vX.Y.Z` with an optional suffix
of `-dev`, `-patch` (`-p`), `-alpha` (`-a`), `-beta` (`-b`) or `-RC`.
The patch, alpha, beta and RC suffixes can also be followed by a number.

Examples:

- 1.0.0
- 1.0.2
- 1.1.0
- 0.2.5
- 1.0.0-dev
- 1.0.0-alpha3
- 1.0.0-beta2
- 1.0.0-RC5
- v2.0.4-p1

Optional if the package repository can infer the version from somewhere, such
as the VCS tag name in the VCS repository. In that case it is also recommended
to omit it.

> **Note:** Packagist uses VCS repositories, so the statement above is very
> much true for Packagist as well. Specifying the version yourself will
> most likely end up creating problems at some point due to human error.

### type

The type of the package. It defaults to `library`.

Package types are used for custom installation logic. If you have a package
that needs some special logic, you can define a custom type. This could be a
`symfony-bundle`, a `wordpress-plugin` or a `typo3-cms-extension`. These types
will all be specific to certain projects, and they will need to provide an
installer capable of installing packages of that type.

Out of the box, Composer supports four types:

- **library:** This is the default. It will copy the files to `vendor`.
- **project:** This denotes a project rather than a library. For example
  application shells like the [Symfony standard edition](https://github.com/symfony/symfony-standard),
  CMSs like the [SilverStripe installer](https://github.com/silverstripe/silverstripe-installer)
  or full fledged applications distributed as packages. This can for example
  be used by IDEs to provide listings of projects to initialize when creating
  a new workspace.
- **metapackage:** An empty package that contains requirements and will trigger
  their installation, but contains no files and will not write anything to the
  filesystem. As such, it does not require a dist or source key to be
  installable.
- **composer-plugin:** A package of type `composer-plugin` may provide an
  installer for other packages that have a custom type. Read more in the
  [dedicated article](articles/custom-installers.md).

Only use a custom type if you need custom logic during installation. It is
recommended to omit this field and have it default to `library`.

### keywords

An array of keywords that the package is related to. These can be used for
searching and filtering.

Examples:

- logging
- events
- database
- redis
- templating

Optional.

### homepage

A URL to the website of the project.

Optional.

### readme

A relative path to the readme document.

Optional.

### time

Release date of the version.

Must be in `YYYY-MM-DD` or `YYYY-MM-DD HH:MM:SS` format.

Optional.

### license

The license of the package. This can be either a string or an array of strings.

The recommended notation for the most common licenses is (alphabetical):

- Apache-2.0
- BSD-2-Clause
- BSD-3-Clause
- BSD-4-Clause
- GPL-2.0-only / GPL-2.0-or-later
- GPL-3.0-only / GPL-3.0-or-later
- LGPL-2.1-only / LGPL-2.1-or-later
- LGPL-3.0-only / LGPL-3.0-or-later
- MIT

Optional, but it is highly recommended to supply this. More identifiers are
listed at the [SPDX Open Source License Registry](https://spdx.org/licenses/).

For closed-source software, you may use `"proprietary"` as the license identifier.

An Example:

```json
{
    "license": "MIT"
}
```

For a package, when there is a choice between licenses ("disjunctive license"),
multiple can be specified as array.

An Example for disjunctive licenses:

```json
{
    "license": [
       "LGPL-2.1-only",
       "GPL-3.0-or-later"
    ]
}
```

Alternatively they can be separated with "or" and enclosed in parentheses;

```json
{
    "license": "(LGPL-2.1-only or GPL-3.0-or-later)"
}
```

Similarly, when multiple licenses need to be applied ("conjunctive license"),
they should be separated with "and" and enclosed in parentheses.

### authors

The authors of the package. This is an array of objects.

Each author object can have following properties:

* **name:** The author's name. Usually their real name.
* **email:** The author's email address.
* **homepage:** An URL to the author's website.
* **role:** The author's role in the project (e.g. developer or translator)

An example:

```json
{
    "authors": [
        {
            "name": "Nils Adermann",
            "email": "naderman@naderman.de",
            "homepage": "https://www.naderman.de",
            "role": "Developer"
        },
        {
            "name": "Jordi Boggiano",
            "email": "j.boggiano@seld.be",
            "homepage": "https://seld.be",
            "role": "Developer"
        }
    ]
}
```

Optional, but highly recommended.

### support

Various information to get support about the project.

Support information includes the following:

* **email:** Email address for support.
* **issues:** URL to the issue tracker.
* **forum:** URL to the forum.
* **wiki:** URL to the wiki.
* **irc:** IRC channel for support, as irc://server/channel.
* **source:** URL to browse or download the sources.
* **docs:** URL to the documentation.
* **rss:** URL to the RSS feed.
* **chat:** URL to the chat channel.

An example:

```json
{
    "support": {
        "email": "support@example.org",
        "irc": "irc://irc.freenode.org/composer"
    }
}
```

Optional.

### funding

A list of URLs to provide funding to the package authors for maintenance and
development of new functionality.

Each entry consists of the following

* **type:** The type of funding, or the platform through which funding can be provided, e.g. patreon, opencollective, tidelift or github.
* **url:** URL to a website with details, and a way to fund the package.

An example:

```json
{
    "funding": [
        {
            "type": "patreon",
            "url": "https://www.patreon.com/phpdoctrine"
        },
        {
            "type": "tidelift",
            "url": "https://tidelift.com/subscription/pkg/packagist-doctrine_doctrine-bundle"
        },
        {
            "type": "other",
            "url": "https://www.doctrine-project.org/sponsorship.html"
        }
    ]
}
```

Optional.

### Package links

All of the following take an object which maps package names to
versions of the package via version constraints. Read more about
versions [here](articles/versions.md).

Example:

```json
{
    "require": {
        "monolog/monolog": "1.0.*"
    }
}
```

All links are optional fields.

`require` and `require-dev` additionally support _stability flags_ ([root-only](04-schema.md#root-package)).
They take the form "_constraint_@_stability flag_".
These allow you to further restrict or expand the stability of a package beyond
the scope of the [minimum-stability](#minimum-stability) setting. You can apply
them to a constraint, or apply them to an empty _constraint_ if you want to
allow unstable packages of a dependency for example.

Example:

```json
{
    "require": {
        "monolog/monolog": "1.0.*@beta",
        "acme/foo": "@dev"
    }
}
```

If one of your dependencies has a dependency on an unstable package you need to
explicitly require it as well, along with its sufficient stability flag.

Example:

Assuming `doctrine/doctrine-fixtures-bundle` requires `"doctrine/data-fixtures": "dev-master"`
then inside the root composer.json you need to add the second line below to allow dev
releases for the `doctrine/data-fixtures` package :

```json
{
    "require": {
        "doctrine/doctrine-fixtures-bundle": "dev-master",
        "doctrine/data-fixtures": "@dev"
    }
}
```

`require` and `require-dev` additionally support explicit references (i.e.
commit) for dev versions to make sure they are locked to a given state, even
when you run update. These only work if you explicitly require a dev version
and append the reference with `#<ref>`. This is also a
[root-only](04-schema.md#root-package) feature and will be ignored in
dependencies.

Example:

```json
{
    "require": {
        "monolog/monolog": "dev-master#2eb0c0978d290a1c45346a1955188929cb4e5db7",
        "acme/foo": "1.0.x-dev#abc123"
    }
}
```

> **Note:** This feature has severe technical limitations, as the
> composer.json metadata will still be read from the branch name you specify
> before the hash. You should therefore only use this as a temporary solution
> during development to remediate transient issues, until you can switch to
> tagged releases. The Composer team does not actively support this feature
> and will not accept bug reports related to it.

It is also possible to inline-alias a package constraint so that it matches
a constraint that it otherwise would not. For more information [see the
aliases article](articles/aliases.md).

`require` and `require-dev` also support references to specific PHP versions
and PHP extensions your project needs to run successfully.

Example:

```json
{
    "require" : {
        "php" : "^5.5 || ^7.0",
        "ext-mbstring": "*"
    }
}
```

> **Note:** It is important to list PHP extensions your project requires.
> Not all PHP installations are created equal: some may miss extensions you
> may consider as standard (such as `ext-mysqli` which is not installed by
> default in Fedora/CentOS minimal installation systems). Failure to list
> required PHP extensions may lead to a bad user experience: Composer will
> install your package without any errors but it will then fail at run-time.
> The `composer show --platform` command lists all PHP extensions available on
> your system. You may use it to help you compile the list of extensions you
> use and require. Alternatively you may use third party tools to analyze
> your project for the list of extensions used.

#### require

Lists packages required by this package. The package will not be installed
unless those requirements can be met.

#### require-dev <span>([root-only](04-schema.md#root-package))</span>

Lists packages required for developing this package, or running
tests, etc. The dev requirements of the root package are installed by default.
Both `install` or `update` support the `--no-dev` option that prevents dev
dependencies from being installed.

#### conflict

Lists packages that conflict with this version of this package. They
will not be allowed to be installed together with your package.

Note that when specifying ranges like `<1.0 >=1.1` in a `conflict` link,
this will state a conflict with all versions that are less than 1.0 *and* equal
or newer than 1.1 at the same time, which is probably not what you want. You
probably want to go for `<1.0 || >=1.1` in this case.

#### replace

Lists packages that are replaced by this package. This allows you to fork a
package, publish it under a different name with its own version numbers, while
packages requiring the original package continue to work with your fork because
it replaces the original package.

This is also useful for packages that contain sub-packages, for example the main
symfony/symfony package contains all the Symfony Components which are also
available as individual packages. If you require the main package it will
automatically fulfill any requirement of one of the individual components,
since it replaces them.

Caution is advised when using replace for the sub-package purpose explained
above. You should then typically only replace using `self.version` as a version
constraint, to make sure the main package only replaces the sub-packages of
that exact version, and not any other version, which would be incorrect.

#### provide

List of other packages that are provided by this package. This is mostly
useful for implementations of common interfaces. A package could depend on
some virtual `logger-implementation` package, any library that implements
this logger interface would list it in `provide`.
Using `provide` with the name of an actual package rather than a virtual one
implies that the code of that package is also shipped, in which case `replace`
is generally a better choice. A common convention for packages providing an
interface and relying on other packages to provide an implementation (for
instance the PSR interfaces) is to use a `-implementation` suffix for the
name of the virtual package corresponding to the interface package.

#### suggest

Suggested packages that can enhance or work well with this package. These are
informational and are displayed after the package is installed, to give
your users a hint that they could add more packages, even though they are not
strictly required.

The format is like package links above, except that the values are free text
and not version constraints.

Example:

```json
{
    "suggest": {
        "monolog/monolog": "Allows more advanced logging of the application flow",
        "ext-xml": "Needed to support XML format in class Foo"
    }
}
```

### autoload

Autoload mapping for a PHP autoloader.

[`PSR-4`](https://www.php-fig.org/psr/psr-4/) and [`PSR-0`](http://www.php-fig.org/psr/psr-0/)
autoloading, `classmap` generation and `files` includes are supported.

PSR-4 is the recommended way since it offers greater ease of use (no need
to regenerate the autoloader when you add classes).

#### PSR-4

Under the `psr-4` key you define a mapping from namespaces to paths, relative to the
package root. When autoloading a class like `Foo\\Bar\\Baz` a namespace prefix
`Foo\\` pointing to a directory `src/` means that the autoloader will look for a
file named `src/Bar/Baz.php` and include it if present. Note that as opposed to
the older PSR-0 style, the prefix (`Foo\\`) is **not** present in the file path.

Namespace prefixes must end in `\\` to avoid conflicts between similar prefixes.
For example `Foo` would match classes in the `FooBar` namespace so the trailing
backslashes solve the problem: `Foo\\` and `FooBar\\` are distinct.

The PSR-4 references are all combined, during install/update, into a single
key => value array which may be found in the generated file
`vendor/composer/autoload_psr4.php`.

Example:

```json
{
    "autoload": {
        "psr-4": {
            "Monolog\\": "src/",
            "Vendor\\Namespace\\": ""
        }
    }
}
```

If you need to search for a same prefix in multiple directories,
you can specify them as an array as such:

```json
{
    "autoload": {
        "psr-4": { "Monolog\\": ["src/", "lib/"] }
    }
}
```

If you want to have a fallback directory where any namespace will be looked for,
you can use an empty prefix like:

```json
{
    "autoload": {
        "psr-4": { "": "src/" }
    }
}
```

#### PSR-0

Under the `psr-0` key you define a mapping from namespaces to paths, relative to the
package root. Note that this also supports the PEAR-style non-namespaced convention.

Please note namespace declarations should end in `\\` to make sure the autoloader
responds exactly. For example `Foo` would match in `FooBar` so the trailing
backslashes solve the problem: `Foo\\` and `FooBar\\` are distinct.

The PSR-0 references are all combined, during install/update, into a single key => value
array which may be found in the generated file `vendor/composer/autoload_namespaces.php`.

Example:

```json
{
    "autoload": {
        "psr-0": {
            "Monolog\\": "src/",
            "Vendor\\Namespace\\": "src/",
            "Vendor_Namespace_": "src/"
        }
    }
}
```

If you need to search for a same prefix in multiple directories,
you can specify them as an array as such:

```json
{
    "autoload": {
        "psr-0": { "Monolog\\": ["src/", "lib/"] }
    }
}
```

The PSR-0 style is not limited to namespace declarations only but may be
specified right down to the class level. This can be useful for libraries with
only one class in the global namespace. If the php source file is also located
in the root of the package, for example, it may be declared like this:

```json
{
    "autoload": {
        "psr-0": { "UniqueGlobalClass": "" }
    }
}
```

If you want to have a fallback directory where any namespace can be, you can
use an empty prefix like:

```json
{
    "autoload": {
        "psr-0": { "": "src/" }
    }
}
```

#### Classmap

The `classmap` references are all combined, during install/update, into a single
key => value array which may be found in the generated file
`vendor/composer/autoload_classmap.php`. This map is built by scanning for
classes in all `.php` and `.inc` files in the given directories/files.

You can use the classmap generation support to define autoloading for all libraries
that do not follow PSR-0/4. To configure this you specify all directories or files
to search for classes.

Example:

```json
{
    "autoload": {
        "classmap": ["src/", "lib/", "Something.php"]
    }
}
```

Wildcards (`*`) are also supported in a classmap paths, and expand to match any directory name:

Example:

```json
{
    "autoload": {
        "classmap": ["src/addons/*/lib/", "3rd-party/*", "Something.php"]
    }
}
```

#### Files

If you want to require certain files explicitly on every request then you can use
the `files` autoloading mechanism. This is useful if your package includes PHP functions
that cannot be autoloaded by PHP.

Example:

```json
{
    "autoload": {
        "files": ["src/MyLibrary/functions.php"]
    }
}
```

#### Exclude files from classmaps

If you want to exclude some files or folders from the classmap you can use the `exclude-from-classmap` property.
This might be useful to exclude test classes in your live environment, for example, as those will be skipped
from the classmap even when building an optimized autoloader.

The classmap generator will ignore all files in the paths configured here. The paths are absolute from the package
root directory (i.e. composer.json location), and support `*` to match anything but a slash, and `**` to
match anything. `**` is implicitly added to the end of the paths.

Example:

```json
{
    "autoload": {
        "exclude-from-classmap": ["/Tests/", "/test/", "/tests/"]
    }
}
```

#### Optimizing the autoloader

The autoloader can have quite a substantial impact on your request time
(50-100ms per request in large frameworks using a lot of classes). See the
[article about optimizing the autoloader](articles/autoloader-optimization.md)
for more details on how to reduce this impact.

### autoload-dev <span>([root-only](04-schema.md#root-package))</span>

This section allows to define autoload rules for development purposes.

Classes needed to run the test suite should not be included in the main autoload
rules to avoid polluting the autoloader in production and when other people use
your package as a dependency.

Therefore, it is a good idea to rely on a dedicated path for your unit tests
and to add it within the autoload-dev section.

Example:

```json
{
    "autoload": {
        "psr-4": { "MyLibrary\\": "src/" }
    },
    "autoload-dev": {
        "psr-4": { "MyLibrary\\Tests\\": "tests/" }
    }
}
```

### include-path

> **DEPRECATED**: This is only present to support legacy projects, and all new code
> should preferably use autoloading. As such it is a deprecated practice, but the
> feature itself will not likely disappear from Composer.

A list of paths which should get appended to PHP's `include_path`.

Example:

```json
{
    "include-path": ["lib/"]
}
```

Optional.

### target-dir

> **DEPRECATED**: This is only present to support legacy PSR-0 style autoloading,
> and all new code should preferably use PSR-4 without target-dir and projects
> using PSR-0 with PHP namespaces are encouraged to migrate to PSR-4 instead.

Defines the installation target.

In case the package root is below the namespace declaration you cannot
autoload properly. `target-dir` solves this problem.

An example is Symfony. There are individual packages for the components. The
Yaml component is under `Symfony\Component\Yaml`. The package root is that
`Yaml` directory. To make autoloading possible, we need to make sure that it
is not installed into `vendor/symfony/yaml`, but instead into
`vendor/symfony/yaml/Symfony/Component/Yaml`, so that the autoloader can load
it from `vendor/symfony/yaml`.

To do that, `autoload` and `target-dir` are defined as follows:

```json
{
    "autoload": {
        "psr-0": { "Symfony\\Component\\Yaml\\": "" }
    },
    "target-dir": "Symfony/Component/Yaml"
}
```

Optional.

### minimum-stability <span>([root-only](04-schema.md#root-package))</span>

This defines the default behavior for filtering packages by stability. This
defaults to `stable`, so if you rely on a `dev` package, you should specify
it in your file to avoid surprises.

All versions of each package are checked for stability, and those that are less
stable than the `minimum-stability` setting will be ignored when resolving
your project dependencies. (Note that you can also specify stability requirements
on a per-package basis using stability flags in the version constraints that you
specify in a `require` block (see [package links](#package-links) for more details).

Available options (in order of stability) are `dev`, `alpha`, `beta`, `RC`,
and `stable`.

### prefer-stable <span>([root-only](04-schema.md#root-package))</span>

When this is enabled, Composer will prefer more stable packages over unstable
ones when finding compatible stable packages is possible. If you require a
dev version or only alphas are available for a package, those will still be
selected granted that the minimum-stability allows for it.

Use `"prefer-stable": true` to enable.

### repositories <span>([root-only](04-schema.md#root-package))</span>

Custom package repositories to use.

By default Composer only uses the packagist repository. By specifying
repositories you can get packages from elsewhere.

Repositories are not resolved recursively. You can only add them to your main
`composer.json`. Repository declarations of dependencies' `composer.json`s are
ignored.

The following repository types are supported:

* **composer:** A Composer repository is a `packages.json` file served
  via the network (HTTP, FTP, SSH), that contains a list of `composer.json`
  objects with additional `dist` and/or `source` information. The `packages.json`
  file is loaded using a PHP stream. You can set extra options on that stream
  using the `options` parameter.
* **vcs:** The version control system repository can fetch packages from git,
  svn, fossil and hg repositories.
* **package:** If you depend on a project that does not have any support for
  composer whatsoever you can define the package inline using a `package`
  repository. You basically inline the `composer.json` object.

For more information on any of these, see [Repositories](05-repositories.md).

Example:

```json
{
    "repositories": [
        {
            "type": "composer",
            "url": "http://packages.example.com"
        },
        {
            "type": "composer",
            "url": "https://packages.example.com",
            "options": {
                "ssl": {
                    "verify_peer": "true"
                }
            }
        },
        {
            "type": "vcs",
            "url": "https://github.com/Seldaek/monolog"
        },
        {
            "type": "package",
            "package": {
                "name": "smarty/smarty",
                "version": "3.1.7",
                "dist": {
                    "url": "https://www.smarty.net/files/Smarty-3.1.7.zip",
                    "type": "zip"
                },
                "source": {
                    "url": "https://smarty-php.googlecode.com/svn/",
                    "type": "svn",
                    "reference": "tags/Smarty_3_1_7/distribution/"
                }
            }
        }
    ]
}
```

> **Note:** Order is significant here. When looking for a package, Composer
will look from the first to the last repository, and pick the first match.
By default Packagist is added last which means that custom repositories can
override packages from it.

Using JSON object notation is also possible. However, JSON key/value pairs
are to be considered unordered so consistent behaviour cannot be guaranteed.

 ```json
{
    "repositories": {
         "foo": {
             "type": "composer",
             "url": "http://packages.foo.com"
         }
    }
}
 ```

### config <span>([root-only](04-schema.md#root-package))</span>

A set of configuration options. It is only used for projects. See
[Config](06-config.md) for a description of each individual option.

### scripts <span>([root-only](04-schema.md#root-package))</span>

Composer allows you to hook into various parts of the installation process
through the use of scripts.

See [Scripts](articles/scripts.md) for events details and examples.

### extra

Arbitrary extra data for consumption by `scripts`.

This can be virtually anything. To access it from within a script event
handler, you can do:

```php
$extra = $event->getComposer()->getPackage()->getExtra();
```

Optional.

### bin

A set of files that should be treated as binaries and symlinked into the `bin-dir`
(from config).

See [Vendor Binaries](articles/vendor-binaries.md) for more details.

Optional.

### archive

A set of options for creating package archives.

The following options are supported:

* **name:** Allows configuring base name for archive.
  By default (if not configured, and `--file` is not passed as command-line argument),
  `preg_replace('#[^a-z0-9-_]#i', '-', name)` is used.

Example:

```json
{
    "name": "org/strangeName",
    "archive": {
        "name": "Strange_name"
    }
}
```

* **exclude:** Allows configuring a list of patterns for excluded paths. The
  pattern syntax matches .gitignore files. A leading exclamation mark (!) will
  result in any matching files to be included even if a previous pattern
  excluded them. A leading slash will only match at the beginning of the project
  relative path. An asterisk will not expand to a directory separator.

Example:

```json
{
    "archive": {
        "exclude": ["/foo/bar", "baz", "/*.test", "!/foo/bar/baz"]
    }
}
```

The example will include `/dir/foo/bar/file`, `/foo/bar/baz`, `/file.php`,
`/foo/my.test` but it will exclude `/foo/bar/any`, `/foo/baz`, and `/my.test`.

Optional.

### abandoned

Indicates whether this package has been abandoned.

It can be boolean or a package name/URL pointing to a recommended alternative.

Examples:

Use `"abandoned": true` to indicates this package is abandoned.
Use `"abandoned": "monolog/monolog"` to indicates this package is abandoned, and the
recommended alternative is  `monolog/monolog`.

Defaults to false.

Optional.

### non-feature-branches

A list of regex patterns of branch names that are non-numeric (e.g. "latest" or something),
that will NOT be handled as feature branches. This is an array of strings.

If you have non-numeric branch names, for example like "latest", "current", "latest-stable"
or something, that do not look like a version number, then Composer handles such branches
as feature branches. This means it searches for parent branches, that look like a version
or ends at special branches (like master), and the root package version number becomes the
version of the parent branch or at least master or something.

To handle non-numeric named branches as versions instead of searching for a parent branch
with a valid version or special branch name like master, you can set patterns for branch
names, that should be handled as dev version branches.

This is really helpful when you have dependencies using "self.version", so that not dev-master,
but the same branch is installed (in the example: latest-testing).

An example:

If you have a testing branch, that is heavily maintained during a testing phase and is
deployed to your staging environment, normally `composer show -s` will give you `versions : * dev-master`.

If you configure `latest-.*` as a pattern for non-feature-branches like this:

```json
{
    "non-feature-branches": ["latest-.*"]
}
```

Then `composer show -s` will give you `versions : * dev-latest-testing`.

Optional.

&larr; [Command-line interface](03-cli.md)  |  [Repositories](05-repositories.md) &rarr;

---

# Repositories

This chapter will explain the concept of packages and repositories, what kinds
of repositories are available, and how they work.

## Concepts

Before we look at the different types of repositories that exist, we need to
understand some basic concepts that Composer is built on.

### Package

Composer is a dependency manager. It installs packages locally. A package is
essentially a directory containing something. In this case it is PHP
code, but in theory it could be anything. And it contains a package
description which has a name and a version. The name and the version are used
to identify the package.

In fact, internally Composer sees every version as a separate package. While
this distinction does not matter when you are using Composer, it's quite
important when you want to change it.

In addition to the name and the version, there is useful metadata. The
information most relevant for installation is the source definition, which
describes where to get the package contents. The package data points to the
contents of the package. And there are two options here: dist and source.

**Dist:** The dist is a packaged version of the package data. Usually a
released version, usually a stable release.

**Source:** The source is used for development. This will usually originate
from a source code repository, such as git. You can fetch this when you want
to modify the downloaded package.

Packages can supply either of these, or even both. Depending on certain
factors, such as user-supplied options and stability of the package, one will
be preferred.

### Repository

A repository is a package source. It's a list of packages/versions. Composer
will look in all your repositories to find the packages your project requires.

By default, only the Packagist.org repository is registered in Composer. You can
add more repositories to your project by declaring them in `composer.json`.

Repositories are only available to the root package and the repositories
defined in your dependencies will not be loaded. Read the
[FAQ entry](faqs/why-can't-composer-load-repositories-recursively.md) if you
want to learn why.

When resolving dependencies, packages are looked up from repositories from
top to bottom, and by default, as soon as a package is found in one, Composer
stops looking in other repositories. Read the
[repository priorities](articles/repository-priorities.md) article for more
details and to see how to change this behavior.

---

## Types

### Composer

The main repository type is the `composer` repository. It uses a single
`packages.json` file that contains all of the package metadata.

This is also the repository type that packagist uses. To reference a
`composer` repository, supply the path before the `packages.json` file.
In the case of packagist, that file is located at `/packages.json`, so the URL of
the repository would be `repo.packagist.org`. For `example.org/packages.json` the
repository URL would be `example.org`.

```json
{
    "repositories": [
        {
            "type": "composer",
            "url": "https://example.org"
        }
    ]
}
```

#### packages

The only required field is `packages`. The JSON structure is as follows:

```json
{
    "packages": {
        "vendor/package-name": {
            "dev-master": { @composer.json },
            "1.0.x-dev": { @composer.json },
            "0.0.1": { @composer.json },
            "1.0.0": { @composer.json }
        }
    }
}
```

The `@composer.json` marker would be the contents of the `composer.json` from
that package version including as a minimum:

* name
* version
* dist or source

Here is a minimal package definition:

```json
{
    "name": "smarty/smarty",
    "version": "3.1.7",
    "dist": {
        "url": "https://www.smarty.net/files/Smarty-3.1.7.zip",
        "type": "zip"
    }
}
```

It may include any of the other fields specified in the [schema](04-schema.md).

#### notify-batch

The `notify-batch` field allows you to specify a URL that will be called
every time a user installs a package. The URL can be either an absolute path
(that will use the same domain as the repository), or a fully qualified URL.

An example value:

```json
{
    "notify-batch": "/downloads/"
}
```

For `example.org/packages.json` containing a `monolog/monolog` package, this
would send a `POST` request to `example.org/downloads/` with following
JSON request body:

```json
{
    "downloads": [
        {"name": "monolog/monolog", "version": "1.2.1.0"}
    ]
}
```

The version field will contain the normalized representation of the version
number.

This field is optional.

#### provider-includes and providers-url

The `provider-includes` field allows you to list a set of files that list
package names provided by this repository. The hash should be a sha256 of
the files in this case.

The `providers-url` describes how provider files are found on the server. It
is an absolute path from the repository root. It must contain the placeholders
`%package%` and `%hash%`.

An example:

```json
{
    "provider-includes": {
        "providers-a.json": {
            "sha256": "f5b4bc0b354108ef08614e569c1ed01a2782e67641744864a74e788982886f4c"
        },
        "providers-b.json": {
            "sha256": "b38372163fac0573053536f5b8ef11b86f804ea8b016d239e706191203f6efac"
        }
    },
    "providers-url": "/p/%package%$%hash%.json"
}
```

Those files contain lists of package names and hashes to verify the file
integrity, for example:

```json
{
    "providers": {
        "acme/foo": {
            "sha256": "38968de1305c2e17f4de33aea164515bc787c42c7e2d6e25948539a14268bb82"
        },
        "acme/bar": {
            "sha256": "4dd24c930bd6e1103251306d6336ac813b563a220d9ca14f4743c032fb047233"
        }
    }
}
```

The file above declares that acme/foo and acme/bar can be found in this
repository, by loading the file referenced by `providers-url`, replacing
`%package%` by the vendor namespaced package name and `%hash%` by the
sha256 field. Those files themselves contain package definitions as
described [above](#packages).

These fields are optional. You probably don't need them for your own custom
repository.

#### stream options

The `packages.json` file is loaded using a PHP stream. You can set extra
options on that stream using the `options` parameter. You can set any valid
PHP stream context option. See [Context options and
parameters](https://php.net/manual/en/context.php) for more information.

### VCS

VCS stands for version control system. This includes versioning systems like
git, svn, fossil or hg. Composer has a repository type for installing packages
from these systems.

#### Loading a package from a VCS repository

There are a few use cases for this. The most common one is maintaining your
own fork of a third party library. If you are using a certain library for your
project, and you decide to change something in the library, you will want your
project to use the patched version. If the library is on GitHub (this is the
case most of the time), you can fork it there and push your changes to
your fork. After that you update the project's `composer.json`. All you have
to do is add your fork as a repository and update the version constraint to
point to your custom branch. In `composer.json`, you should prefix your custom
branch name with `"dev-"`. For version constraint naming conventions see
[Libraries](02-libraries.md) for more information.

Example assuming you patched monolog to fix a bug in the `bugfix` branch:

```json
{
    "repositories": [
        {
            "type": "vcs",
            "url": "https://github.com/igorw/monolog"
        }
    ],
    "require": {
        "monolog/monolog": "dev-bugfix"
    }
}
```

When you run `php composer.phar update`, you should get your modified version
of `monolog/monolog` instead of the one from packagist.

Note that you should not rename the package unless you really intend to fork
it in the long term, and completely move away from the original package.
Composer will correctly pick your package over the original one since the
custom repository has priority over packagist. If you want to rename the
package, you should do so in the default (often master) branch and not in a
feature branch, since the package name is taken from the default branch.

Also note that the override will not work if you change the `name` property
in your forked repository's `composer.json` file as this needs to match the
original for the override to work.

If other dependencies rely on the package you forked, it is possible to
inline-alias it so that it matches a constraint that it otherwise would not.
For more information [see the aliases article](articles/aliases.md).

#### Using private repositories

Exactly the same solution allows you to work with your private repositories at
GitHub and BitBucket:

```json
{
    "require": {
        "vendor/my-private-repo": "dev-master"
    },
    "repositories": [
        {
            "type": "vcs",
            "url":  "git@bitbucket.org:vendor/my-private-repo.git"
        }
    ]
}
```

The only requirement is the installation of SSH keys for a git client.

#### Git alternatives

Git is not the only version control system supported by the VCS repository.
The following are supported:

* **Git:** [git-scm.com](https://git-scm.com)
* **Subversion:** [subversion.apache.org](https://subversion.apache.org)
* **Mercurial:** [mercurial-scm.org](https://www.mercurial-scm.org)
* **Fossil**: [fossil-scm.org](https://www.fossil-scm.org/)

To get packages from these systems you need to have their respective clients
installed. That can be inconvenient. And for this reason there is special
support for GitHub and BitBucket that use the APIs provided by these sites, to
fetch the packages without having to install the version control system. The
VCS repository provides `dist`s for them that fetch the packages as zips.

* **GitHub:** [github.com](https://github.com) (Git)
* **BitBucket:** [bitbucket.org](https://bitbucket.org) (Git and Mercurial)

The VCS driver to be used is detected automatically based on the URL. However,
should you need to specify one for whatever reason, you can use `git-bitbucket`,
`hg-bitbucket`, `github`, `gitlab`, `perforce`, `fossil`, `git`, `svn` or `hg`
as the repository type instead of `vcs`.

If you set the `no-api` key to `true` on a github repository it will clone the
repository as it would with any other git repository instead of using the
GitHub API. But unlike using the `git` driver directly, Composer will still
attempt to use github's zip files.

Please note:
* **To let Composer choose which driver to use** the repository type needs to be defined as "vcs"
* **If you already used a private repository**, this means Composer should have cloned it in cache. If you want to install the same package with drivers, remember to launch the command `composer clearcache` followed by the command `composer update` to update composer cache and install the package from dist.

#### BitBucket Driver Configuration

> **Note that the repository endpoint for BitBucket needs to be https rather than git.**

After setting up your bitbucket repository, you will also need to
[set up authentication](articles/authentication-for-private-packages.md#bitbucket-oauth).

#### Subversion Options

Since Subversion has no native concept of branches and tags, Composer assumes
by default that code is located in `$url/trunk`, `$url/branches` and
`$url/tags`. If your repository has a different layout you can change those
values. For example if you used capitalized names you could configure the
repository like this:

```json
{
    "repositories": [
        {
            "type": "vcs",
            "url": "http://svn.example.org/projectA/",
            "trunk-path": "Trunk",
            "branches-path": "Branches",
            "tags-path": "Tags"
        }
    ]
}
```

If you have no branches or tags directory you can disable them entirely by
setting the `branches-path` or `tags-path` to `false`.

If the package is in a sub-directory, e.g. `/trunk/foo/bar/composer.json` and
`/tags/1.0/foo/bar/composer.json`, then you can make Composer access it by
setting the `"package-path"` option to the sub-directory, in this example it
would be `"package-path": "foo/bar/"`.

If you have a private Subversion repository you can save credentials in the
http-basic section of your config (See [Schema](04-schema.md)):

```json
{
    "http-basic": {
        "svn.example.org": {
            "username": "username",
            "password": "password"
        }
    }
}
```

If your Subversion client is configured to store credentials by default these
credentials will be saved for the current user and existing saved credentials
for this server will be overwritten. To change this behavior by setting the
`"svn-cache-credentials"` option in your repository configuration:

```json
{
    "repositories": [
        {
            "type": "vcs",
            "url": "http://svn.example.org/projectA/",
            "svn-cache-credentials": false
        }
    ]
}
```

### Package

If you want to use a project that does not support Composer through any of the
means above, you still can define the package yourself by using a `package`
repository.

Basically, you define the same information that is included in the `composer`
repository's `packages.json`, but only for a single package. Again, the
minimum required fields are `name`, `version`, and either of `dist` or
`source`.

Here is an example for the smarty template engine:

```json
{
    "repositories": [
        {
            "type": "package",
            "package": {
                "name": "smarty/smarty",
                "version": "3.1.7",
                "dist": {
                    "url": "https://www.smarty.net/files/Smarty-3.1.7.zip",
                    "type": "zip"
                },
                "source": {
                    "url": "http://smarty-php.googlecode.com/svn/",
                    "type": "svn",
                    "reference": "tags/Smarty_3_1_7/distribution/"
                },
                "autoload": {
                    "classmap": ["libs/"]
                }
            }
        }
    ],
    "require": {
        "smarty/smarty": "3.1.*"
    }
}
```

Typically, you would leave the source part off, as you don't really need it.

> **Note**: This repository type has a few limitations and should be avoided
> whenever possible:
>
> - Composer will not update the package unless you change the `version` field.
> - Composer will not update the commit references, so if you use `master` as
>   reference you will have to delete the package to force an update, and will
>   have to deal with an unstable lock file.

The `"package"` key in a `package` repository may be set to an array to define multiple versions of a package:

```json
{
    "repositories": [
        {
            "type": "package",
            "package": [
                {
                    "name": "foo/bar",
                    "version": "1.0.0",
                    ...
                },
                {
                    "name": "foo/bar",
                    "version": "2.0.0",
                    ...
                }
            ]
        }
    ]
}
```

---

## Hosting your own

While you will probably want to put your packages on packagist most of the
time, there are some use cases for hosting your own repository.

* **Private company packages:** If you are part of a company that uses Composer
  for their packages internally, you might want to keep those packages private.

* **Separate ecosystem:** If you have a project which has its own ecosystem,
  and the packages aren't really reusable by the greater PHP community, you
  might want to keep them separate to packagist. An example of this would be
  wordpress plugins.

For hosting your own packages, a native `composer` type of repository is
recommended, which provides the best performance.

There are a few tools that can help you create a `composer` repository.

### Private Packagist

[Private Packagist](https://packagist.com/) is a hosted or self-hosted
application providing private package hosting as well as mirroring of
GitHub, Packagist.org and other package repositories.

Check out [Packagist.com](https://packagist.com/) for more information.

### Satis

Satis is a static `composer` repository generator. It is a bit like an ultra-
lightweight, static file-based version of packagist.

You give it a `composer.json` containing repositories, typically VCS and
package repository definitions. It will fetch all the packages that are
`require`d and dump a `packages.json` that is your `composer` repository.

Check [the satis GitHub repository](https://github.com/composer/satis) and
the [handling private packages article](articles/handling-private-packages.md) for more
information.

### Artifact

There are some cases, when there is no ability to have one of the previously
mentioned repository types online, even the VCS one. A typical example could be
cross-organisation library exchange through build artifacts. Of course, most
of the time these are private. To use these archives as-is, one can use a
repository of type `artifact` with a folder containing ZIP or TAR archives of
those private packages:

```json
{
    "repositories": [
        {
            "type": "artifact",
            "url": "path/to/directory/with/zips/"
        }
    ],
    "require": {
        "private-vendor-one/core": "15.6.2",
        "private-vendor-two/connectivity": "*",
        "acme-corp/parser": "10.3.5"
    }
}
```

Each zip artifact is a ZIP archive with `composer.json` in root folder:

```sh
unzip -l acme-corp-parser-10.3.5.zip

composer.json
...
```

If there are two archives with different versions of a package, they are both
imported. When an archive with a newer version is added in the artifact folder
and you run `update`, that version will be imported as well and Composer will
update to the latest version.

### Path

In addition to the artifact repository, you can use the path one, which allows
you to depend on a local directory, either absolute or relative. This can be
especially useful when dealing with monolithic repositories.

For instance, if you have the following directory structure in your repository:
```
...
├── apps
│   └── my-app
│       └── composer.json
├── packages
│   └── my-package
│       └── composer.json
...
```

Then, to add the package `my/package` as a dependency, in your
`apps/my-app/composer.json` file, you can use the following configuration:

```json
{
    "repositories": [
        {
            "type": "path",
            "url": "../../packages/my-package"
        }
    ],
    "require": {
        "my/package": "*"
    }
}
```

If the package is a local VCS repository, the version may be inferred by
the branch or tag that is currently checked out. Otherwise, the version should
be explicitly defined in the package's `composer.json` file. If the version
cannot be resolved by these means, it is assumed to be `dev-master`.

When the version cannot be inferred from the local VCS repository, you should use
the special `branch-version` entry under `extra` instead of `version`:

```json
{
    "extra": {
        "branch-version": "4.2-dev"
    }
}
```

The local package will be symlinked if possible, in which case the output in
the console will read `Symlinking from ../../packages/my-package`. If symlinking
is _not_ possible the package will be copied. In that case, the console will
output `Mirrored from ../../packages/my-package`.

Instead of default fallback strategy you can force to use symlink with
`"symlink": true` or mirroring with `"symlink": false` option. Forcing
mirroring can be useful when deploying or generating package from a
monolithic repository.

> **Note:** On Windows, directory symlinks are implemented using NTFS junctions
> because they can be created by non-admin users. Mirroring will always be used
> on versions below Windows 7 or if `proc_open` has been disabled.

```json
{
    "repositories": [
        {
            "type": "path",
            "url": "../../packages/my-package",
            "options": {
                "symlink": false
            }
        }
    ]
}
```

Leading tildes are expanded to the current user's home folder, and environment
variables are parsed in both Windows and Linux/Mac notations. For example
`~/git/mypackage` will automatically load the mypackage clone from
`/home/<username>/git/mypackage`, equivalent to `$HOME/git/mypackage` or
`%USERPROFILE%/git/mypackage`.

> **Note:** Repository paths can also contain wildcards like `*` and `?`.
> For details, see the [PHP glob function](https://php.net/glob).

---

## Disabling Packagist.org

You can disable the default Packagist.org repository by adding this to your
`composer.json`:

```json
{
    "repositories": [
        {
            "packagist.org": false
        }
    ]
}
```

You can disable Packagist.org globally by using the global config flag:

```bash
composer config -g repo.packagist false
```

&larr; [Schema](04-schema.md)  |  [Config](06-config.md) &rarr;

---

# Config

This chapter will describe the `config` section of the `composer.json`
[schema](04-schema.md).

---

## process-timeout

The timeout in seconds for process executions, defaults to 300 (5mins).
The duration processes like git clones can run before
Composer assumes they died out. You may need to make this higher if you have a
slow connection or huge vendors.

To disable the process timeout on a custom command under `scripts`, a static
helper is available:

```json
{
    "scripts": {
        "test": [
            "Composer\\Config::disableProcessTimeout",
            "phpunit"
        ]
    }
}
```

---

## use-include-path

Defaults to `false`. If `true`, the Composer autoloader will also look for classes
in the PHP include path.

---

## preferred-install

Defaults to `auto` and can be any of `source`, `dist` or `auto`. This option
allows you to set the install method Composer will prefer to use. Can
optionally be a hash of patterns for more granular install preferences.

```json
{
    "config": {
        "preferred-install": {
            "my-organization/stable-package": "dist",
            "my-organization/*": "source",
            "partner-organization/*": "auto",
            "*": "dist"
        }
    }
}
```

> **Note:** Order matters. More specific patterns should be earlier than
> more relaxed patterns. When mixing the string notation with the hash
> configuration in global and package configurations the string notation
> is translated to a `*` package pattern.

---

## store-auths

What to do after prompting for authentication, one of: `true` (always store),
`false` (do not store) and `"prompt"` (ask every time), defaults to `"prompt"`.

---

## github-protocols

Defaults to `["https", "ssh", "git"]`. A list of protocols to use when cloning
from github.com, in priority order. By default `git` is present but only if [secure-http](#secure-http)
is disabled, as the git protocol is not encrypted. If you want your origin remote
push URLs to be using https and not ssh (`git@github.com:...`), then set the protocol
list to be only `["https"]` and Composer will stop overwriting the push URL to an ssh
URL.

---

## github-oauth

A list of domain names and oauth keys. For example using `{"github.com":
"oauthtoken"}` as the value of this option will use `oauthtoken` to access
private repositories on github and to circumvent the low IP-based rate limiting
of their API. Composer may prompt for credentials when needed, but these can also be
manually set. Read more on how to get an OAuth token for GitHub and cli syntax
[here](articles/authentication-for-private-packages.md#github-oauth).

---

## gitlab-oauth

A list of domain names and oauth keys. For example using `{"gitlab.com":
"oauthtoken"}` as the value of this option will use `oauthtoken` to access
private repositories on gitlab. Please note: If the package is not hosted at
gitlab.com the domain names must be also specified with the
[`gitlab-domains`](06-config.md#gitlab-domains) option.
Further info can also be found [here](articles/authentication-for-private-packages.md#gitlab-oauth)

---

## gitlab-token

A list of domain names and private tokens. Private token can be either simple
string, or array with username and token. For example using `{"gitlab.com":
"privatetoken"}` as the value of this option will use `privatetoken` to access
private repositories on gitlab. Using `{"gitlab.com": {"username": "gitlabuser",
 "token": "privatetoken"}}` will use both username and token for gitlab deploy
token functionality (https://docs.gitlab.com/ee/user/project/deploy_tokens/)
Please note: If the package is not hosted at
gitlab.com the domain names must be also specified with the
[`gitlab-domains`](06-config.md#gitlab-domains) option. The token must have
`api` or `read_api` scope.
Further info can also be found [here](articles/authentication-for-private-packages.md#gitlab-token)

---

## disable-tls

Defaults to `false`. If set to true all HTTPS URLs will be tried with HTTP
instead and no network level encryption is performed. Enabling this is a
security risk and is NOT recommended. The better way is to enable the
php_openssl extension in php.ini. Enabling this will implicitly disable the
`secure-http` option.

---

## secure-http

Defaults to `true`. If set to true only HTTPS URLs are allowed to be
downloaded via Composer. If you really absolutely need HTTP access to something
then you can disable it, but using [Let's Encrypt](https://letsencrypt.org/) to
get a free SSL certificate is generally a better alternative.

---

## bitbucket-oauth

A list of domain names and consumers. For example using `{"bitbucket.org":
{"consumer-key": "myKey", "consumer-secret": "mySecret"}}`.
Read more [here](articles/authentication-for-private-packages.md#bitbucket-oauth).

---

## cafile

Location of Certificate Authority file on local filesystem. In PHP 5.6+ you
should rather set this via openssl.cafile in php.ini, although PHP 5.6+ should
be able to detect your system CA file automatically.

---

## capath

If cafile is not specified or if the certificate is not found there, the
directory pointed to by capath is searched for a suitable certificate.
capath must be a correctly hashed certificate directory.

---

## http-basic

A list of domain names and username/passwords to authenticate against them. For
example using `{"example.org": {"username": "alice", "password": "foo"}}` as the
value of this option will let Composer authenticate against example.org.
More info can be found [here](articles/authentication-for-private-packages.md#http-basic).

---

## bearer

A list of domain names and tokens to authenticate against them. For example using
`{"example.org": "foo"}` as the value of this option will let Composer authenticate
against example.org using an `Authorization: Bearer foo` header.

---

## platform

Lets you fake platform packages (PHP and extensions) so that you can emulate a
production env or define your target platform in the config. Example: `{"php":
"7.0.3", "ext-something": "4.0.3"}`.

This will make sure that no package requiring more than PHP 7.0.3 can be installed
regardless of the actual PHP version you run locally. However it also means
the dependencies are not checked correctly anymore, if you run PHP 5.6 it will
install fine as it assumes 7.0.3, but then it will fail at runtime.

Therefore if you use this it is recommended, and safer, to also run the
[`check-platform-reqs`](03-cli.md#check-platform-reqs) command as part of your
deployment strategy.

If a dependency requires some extension that you do not have installed locally
you may ignore it instead by passing `--ignore-platform-req=ext-foo` to `update`,
`install` or `require`. In the long run though you should install required
extensions as if you ignore one now and a new package you add a month later also
requires it, you may introduce issues in production unknowingly.

---

## vendor-dir

Defaults to `vendor`. You can install dependencies into a different directory if
you want to. `$HOME` and `~` will be replaced by your home directory's path in
vendor-dir and all `*-dir` options below.

---

## bin-dir

Defaults to `vendor/bin`. If a project includes binaries, they will be symlinked
into this directory.

---

## data-dir

Defaults to `C:\Users\<user>\AppData\Roaming\Composer` on Windows,
`$XDG_DATA_HOME/composer` on unix systems that follow the XDG Base Directory
Specifications, and `$home` on other unix systems. Right now it is only
used for storing past composer.phar files to be able to rollback to older
versions. See also [COMPOSER_HOME](03-cli.md#composer-home).

---

## cache-dir

Defaults to `C:\Users\<user>\AppData\Local\Composer` on Windows,
`$XDG_CACHE_HOME/composer` on unix systems that follow the XDG Base Directory
Specifications, and `$home/cache` on other unix systems. Stores all the caches
used by Composer. See also [COMPOSER_HOME](03-cli.md#composer-home).

---

## cache-files-dir

Defaults to `$cache-dir/files`. Stores the zip archives of packages.

---

## cache-repo-dir

Defaults to `$cache-dir/repo`. Stores repository metadata for the `composer`
type and the VCS repos of type `svn`, `fossil`, `github` and `bitbucket`.

---

## cache-vcs-dir

Defaults to `$cache-dir/vcs`. Stores VCS clones for loading VCS repository
metadata for the `git`/`hg` types and to speed up installs.

---

## cache-files-ttl

Defaults to `15552000` (6 months). Composer caches all dist (zip, tar, ...)
packages that it downloads. Those are purged after six months of being unused by
default. This option allows you to tweak this duration (in seconds) or disable
it completely by setting it to 0.

---

## cache-files-maxsize

Defaults to `300MiB`. Composer caches all dist (zip, tar, ...) packages that it
downloads. When the garbage collection is periodically ran, this is the maximum
size the cache will be able to use. Older (less used) files will be removed
first until the cache fits.

---

## cache-read-only

Defaults to `false`. Whether to use the Composer cache in read-only mode.

---

## bin-compat

Defaults to `auto`. Determines the compatibility of the binaries to be installed.
If it is `auto` then Composer only installs .bat proxy files when on Windows. If
set to `full` then both .bat files for Windows and scripts for Unix-based
operating systems will be installed for each binary. This is mainly useful if you
run Composer inside a linux VM but still want the .bat proxies available for use
in the Windows host OS.

---

## prepend-autoloader

Defaults to `true`. If `false`, the Composer autoloader will not be prepended to
existing autoloaders. This is sometimes required to fix interoperability issues
with other autoloaders.

---

## autoloader-suffix

Defaults to `null`. String to be used as a suffix for the generated Composer
autoloader. When null a random one will be generated.

---

## optimize-autoloader

Defaults to `false`. If `true`, always optimize when dumping the autoloader.

---

## sort-packages

Defaults to `false`. If `true`, the `require` command keeps packages sorted
by name in `composer.json` when adding a new package.

---

## classmap-authoritative

Defaults to `false`. If `true`, the Composer autoloader will only load classes
from the classmap. Implies `optimize-autoloader`.

---

## apcu-autoloader

Defaults to `false`. If `true`, the Composer autoloader will check for APCu and
use it to cache found/not-found classes when the extension is enabled.

---

## github-domains

Defaults to `["github.com"]`. A list of domains to use in github mode. This is
used for GitHub Enterprise setups.

---

## github-expose-hostname

Defaults to `true`. If `false`, the OAuth tokens created to access the
github API will have a date instead of the machine hostname.

---

## gitlab-domains

Defaults to `["gitlab.com"]`. A list of domains of GitLab servers.
This is used if you use the `gitlab` repository type.

---

## use-github-api

Defaults to `true`.  Similar to the `no-api` key on a specific repository,
setting `use-github-api` to `false` will define the global behavior for all
GitHub repositories to clone the repository as it would with any other git
repository instead of using the GitHub API. But unlike using the `git`
driver directly, Composer will still attempt to use GitHub's zip files.

---

## notify-on-install

Defaults to `true`. Composer allows repositories to define a notification URL,
so that they get notified whenever a package from that repository is installed.
This option allows you to disable that behavior.

---

## discard-changes

Defaults to `false` and can be any of `true`, `false` or `"stash"`. This option
allows you to set the default style of handling dirty updates when in
non-interactive mode. `true` will always discard changes in vendors, while
`"stash"` will try to stash and reapply. Use this for CI servers or deploy
scripts if you tend to have modified vendors.

---

## archive-format

Defaults to `tar`. Overrides the default format used by the archive command.

---

## archive-dir

Defaults to `.`. Default destination for archives created by the archive
command.

Example:

```json
{
    "config": {
        "archive-dir": "/home/user/.composer/repo"
    }
}
```

---

## htaccess-protect

Defaults to `true`. If set to `false`, Composer will not create `.htaccess` files
in the composer home, cache, and data directories.

## lock

Defaults to `true`. If set to `false`, Composer will not create a `composer.lock`
file.

---

## platform-check

Defaults to `php-only` which only checks the PHP version. Set to `true` to also
check the presence of extension. If set to `false`, Composer will not create and
require a `platform_check.php` file as part of the autoloader bootstrap.

&larr; [Repositories](05-repositories.md)  |  [Runtime](07-runtime.md) &rarr;

---

---

# Runtime Composer utilities

While Composer is mostly used around your project to install its dependencies,
there are a few things which are made available to you at runtime.

If you need to rely on some of these in a specific version, you can require
the `composer-runtime-api` package.

---

## Autoload

The autoloader is the most used one, and is already covered in our
[basic usage guide](01-basic-usage.md#autoloading). It is available in all
Composer versions.

---

## Installed versions

composer-runtime-api 2.0 introduced a new `Composer\InstalledVersions` class which offers
a few static methods to inspect which versions are currently installed. This is
automatically available to your code as long as you include the Composer autoloader.

The main use cases for this class are the following:

### Knowing whether package X (or virtual package) is present

```php
\Composer\InstalledVersions::isInstalled('vendor/package'); // returns bool
\Composer\InstalledVersions::isInstalled('psr/log-implementation'); // returns bool
```

Note that this can not be used to check whether platform packages are installed.

### Knowing whether package X is installed in version Y

> **Note:** To use this, your package must require `"composer/semver": "^2.0"`.

```php
use Composer\Semver\VersionParser;

\Composer\InstalledVersions::satisfies(new VersionParser, 'vendor/package', '2.0.*');
\Composer\InstalledVersions::satisfies(new VersionParser, 'psr/log-implementation', '^1.0');
```

This will return true if e.g. vendor/package is installed in a version matching
`2.0.*`, but also if the given package name is replaced or provided by some other
package.

### Knowing the version of package X

> **Note:** This will return `null` if the package name you ask for is not itself installed
> but merely provided or replaced by another package. We therefore recommend using satisfies()
> in library code at least. In application code you have a bit more control and it is less
> important.

```php
// returns a normalized version (e.g. 1.2.3.0) if vendor/package is installed,
// or null if it is provided/replaced,
// or throws OutOfBoundsException if the package is not installed at all
\Composer\InstalledVersions::getVersion('vendor/package');
```

```php
// returns the original version (e.g. v1.2.3) if vendor/package is installed,
// or null if it is provided/replaced,
// or throws OutOfBoundsException if the package is not installed at all
\Composer\InstalledVersions::getPrettyVersion('vendor/package');
```

```php
// returns the package dist or source reference (e.g. a git commit hash) if vendor/package is installed,
// or null if it is provided/replaced,
// or throws OutOfBoundsException if the package is not installed at all
\Composer\InstalledVersions::getReference('vendor/package');
```

### Knowing a package's own installed version

If you are only interested in getting a package's own version, e.g. in the source of acme/foo you want
to know which version acme/foo is currently running to display that to the user, then it is
acceptable to use getVersion/getPrettyVersion/getReference.

The warning in the section above does not apply in this case as you are sure the package is present
and not being replaced if your code is running.

It is nonetheless a good idea to make sure you handle the `null` return value as gracefully as
possible for safety.

A few other methods are available for more complex usages, please refer to the
source/docblocks of the class itself.

---

## Platform check

composer-runtime-api 2.0 introduced a new `vendor/composer/platform_check.php` file, which
is included automatically when you include the Composer autoloader.

It verifies that platform requirements (i.e. php and php extensions) are fulfilled
by the PHP process currently running. If the requirements are not met, the script
prints a warning with the missing requirements and exits with code 104.

To avoid an unexpected white page of death with some obscure PHP extension warning in
production, you can run `composer check-platform-reqs` as part of your
deployment/build and if that returns a non-0 code you should abort.

The default value is `php-only` which only checks the PHP version.

If you for some reason do not want to use this safety check, and would rather
risk runtime errors when your code executes, you can disable this by setting the
[`platform-check`](06-config.md#platform-check) config option to `false`.

If you want the check to include verifying the presence of PHP extensions,
set the config option to `true`.

&larr; [Config](06-config.md)  |  [Community](08-community.md) &rarr;

---
