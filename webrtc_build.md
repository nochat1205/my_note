











ref: https://webrtc.github.io/webrtc-org/native-code/development/





### gn

```
gn gen out/Default
```

#### get gn

For Chromium and Chromium-based projects, there is a script in `depot_tools`, which is presumably in your PATH, with this name. The script will find the binary in the source tree containing the current directory and run it.

#### run gn



**make a build directory:**

```
gn gen out/my_build

```

**Passing build arguments:**  传入参数

```
gn args out/my_build
```

This will bring up an editor. Type build args into that file like this:

```
is_component_build = true
is_debug = false
```

The available variables will depend on your build (this example is from Chromium). You can see the list of available arguments and their default values by typing

```
gn args --list out/my_build
```

Chrome developers can also read the [Chrome-specific build configuration](http://www.chromium.org/developers/gn-build-configuration) instructions for more information.



#### Cross-compiling to a target OS or architecture  交叉编译

Run `gn args out/Default` (substituting your build directory as needed) and add one or more of the following lines for common cross-compiling options.

```
target_os = "chromeos"
target_os = "android"

target_cpu = "arm"
target_cpu = "x86"
target_cpu = "x64"
```



### step by step

In that directory there is a `tutorial` directory. There is already a `tutorial.cc` file that's not hooked up to the build. Create a new `BUILD.gn` file in that directory for our new target.



```
executable("tutorial") {
  sources = [
    "tutorial.cc",
  ]
}
```



Now we just need to tell the build about this new target. Open the `BUILD.gn` file in the parent (`simple_build`) directory. GN starts by loading this root file, and then loads all dependencies ourward from here, so we just need to add a reference to our new target from this file.

You could add our new target as a dependency from one of the existing targets in the `simple_build/BUILD.gn` file, but it usually doesn‘t make a lot of sense to have an executable as a depdency of another executable (they can’t be linked). So let‘s make a “tools” group. In GN, a “group” is **just a collection of dependencies that’s not complied or linked**:

```
group("tools") {
  deps = [
    # This will expand to the name "//tutorial:tutorial" which is the full name
    # of our new target. Run "gn help labels" for more.
    "//tutorial",
  ]
}
```

**Testing your addition:**

From the command line in the `simple_build` directory:

```
gn gen out
ninja -C out tutorial
out/tutorial
```

Side note: GN encourages target names for static libraries that aren't globally unique. To build one of these, you can pass the label with its path (but no leading “//”) to ninja:

```
ninja -C out some/path/to/target:my_target
```



##### Declaring dependencies

Let's look at the targets defined in [examples/simple_build/BUILD.gn](https://gn.googlesource.com/gn/+/HEAD/examples/simple_build/BUILD.gn). There is a static library that defines one function, `GetStaticText()`:



```
static_library("hello_static") {
  sources = [
    "hello_static.cc",
    "hello_static.h",
  ]
}
```



There is also a shared library that defines one function `GetSharedText()`:

```
shared_library("hello_shared") {
  sources = [
    "hello_shared.cc",
    "hello_shared.h",
  ]

  defines = [ "HELLO_SHARED_IMPLEMENTATION" ]
}
```

This also illustrates how to set preprocessor defines for a target. To set more than one or to assign values, use this form:

```
defines = [
  "HELLO_SHARED_IMPLEMENTATION",
  "ENABLE_DOOM_MELON=0",
]
```



Now let's look at the executable that depends on these two libraries:

```
executable("hello") {
  sources = [
    "hello.cc",
  ]

  deps = [
    ":hello_shared",
    ":hello_static",
  ]
}
```

This executable includes one source file and depends on the previous two libraries. **Labels starting with a colon** refer to a target with that name in the current BUILD.gn file.



**Putting settings in a config :**

Users of a library often need compiler flags, defines, and include directories applied to them. To do this, put all such settings into a “config” which is a named collection of settings (but not sources or dependencies):

```
config("my_lib_config") {
  defines = [ "ENABLE_DOOM_MELON" ]
  include_dirs = [ "//third_party/something" ]
}
```

To apply a config's settings to a target, add it to the `configs` list:

```gn
static_library("hello_shared") {
  ...
  # Note "+=" here is usually required, see "default configs" below.
  configs += [
    ":my_lib_config",
  ]
}
```

A config can be applied to all targets that depend on the current one by putting its label in the `public_configs` list:

```
static_library("hello_shared") {
  ...
  public_configs = [
    ":my_lib_config",
  ]
}
```

The `public_configs` also applies to the current target, so there's no need to list a config in both places.

**Default configs:**

```
executable("hello") {
  print(configs)
}
```

目标可以修改此列表以更改其默认值。例如，构建设置可能会通过添加 `no_exceptions` 配置来默认关闭异常，但目标可能会通过将其替换为其他配置来重新启用异常：

```
executable("hello") {
  ...
  configs -= [ "//build:no_exceptions" ]  # Remove global default.
  configs += [ "//build:exceptions" ]  # Replace with a different one.
}
```

上面的打印命令也可以使用字符串插值来表达。这是一种将值转换为字符串的方法。它使用符号“$”来引用变量：

```
print("The configs for the target $target_name are $configs")
```

###### **Add a new build argument:**

 You declare which arguments you accept and specify default values via `declare_args`.

```
declare_args() {
  enable_teleporter = true
  enable_doom_melon = false
}
```

See `gn help buildargs` for an overview of how this works. See `gn help declare_args` for specifics on declaring them.

It is an error to declare a given argument more than once in a given scope, so care should be used in scoping and naming arguments.

###### Don‘t know what’s going on? 

You can run GN in verbose mode to see lots of messages about what it's doing. Use `-v` for this.

The “desc” command

You can run `gn desc <build_dir> <targetname>` to get information about a given target:

```
gn desc out/Default //foo/bar:say_hello
```

will print out lots of exciting information. You can also print just one section. Lets say you wanted to know where your `TWO_PEOPLE` define came from on the `say_hello` target:

```
> gn desc out/Default //foo/bar:say_hello defines --blame
...lots of other stuff omitted...
  From //foo/bar:hello_config
       (Added by //foo/bar/BUILD.gn:12)
    TWO_PEOPLE
```



Another particularly interesting variation:

```
gn desc out/Default //base:base_i18n deps --tree
```

See `gn help desc` for more. 



### ninja

- -C DIR   change to DIR before doing anything else
- -t TOOL  run a subtool (use '-t list' to list subtools)



ninja subtools:
     browse  browse dependency graph in a web browser
      clean  clean built files
   commands  list all commands required to rebuild given targets
     inputs  list all inputs required to rebuild given targets
       deps  show dependencies stored in the deps log
missingdeps  check deps log dependencies on generated files
      graph  output graphviz dot file for targets
      query  show inputs/outputs for a path
    targets  list targets by their rule or depth in the DAG
     compdb  dump JSON compilation database to stdout
  recompact  recompacts ninja-internal data structures
     restat  restats all outputs in the build log
      rules  list all rules
  cleandead  clean built files that are no longer produced by the manifest

```
ninja -C out -t list
```



