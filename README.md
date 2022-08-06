# Bazel rules_nodejs Import Error

In this case, it seems Bazel is doing something different with the `node_modules/`
at build time than `yarn` or `npm`.

I spent a lot of time trying to reproduce the problem with yarn by changing the
`node_modules/` in manual testing to be more like the `node_modules/`
produced by Bazel, but I was unable. I'm not sure what the problem could be.

## Testing with yarn

Setup:

```sh
function yarn { bazel run @yarn//:yarn -- --cwd=$PWD "$@" }
yarn install
```

Vitest works with npm with global imports:

```sh
yarn run test:unit run --config=$PWD/global_vite.config.ts global_import.test.ts
```

---

Vitest works with npm without global imports:

```sh
yarn run test:unit run --config=$PWD/static_vite.config.ts static_import.test.ts
```

## Testing with Bazel

### Yarn Toolchain

Vitest works with bazel with global imports:

```sh
bazel test --define=VERBOSE_LOGS=1 //:global_unit_test --test_output=all
```

---

Vitest fails with bazel without global imports:

```sh
bazel test --define=VERBOSE_LOGS=1 //:static_unit_test --test_output=all
```

---

### npm Toolchain

Vitest fails with bazel without global imports using npm instead of Yarn too:

```sh
bazel test --define=VERBOSE_LOGS=1 //:static_unit_test_npm --test_output=all
```

# More Information

This is the contents of the generated bzl for vitest:

`bazel-bazel_vitest_error_demo/external/npm/vitest/index.bzl`

```bzl
load("@build_bazel_rules_nodejs//:index.bzl", "nodejs_binary", "nodejs_test", "npm_package_bin")

# Generated helper macro to call vitest
def vitest(**kwargs):
    output_dir = kwargs.pop("output_dir", False)
    if "outs" in kwargs or output_dir:
        npm_package_bin(tool = "@npm//vitest/bin:vitest", output_dir = output_dir, **kwargs)
    else:
        nodejs_binary(
            entry_point = { "@npm//:node_modules/vitest": "vitest.mjs" },
            data = ["@npm//vitest:vitest"] + kwargs.pop("data", []),
            **kwargs
        )

# Just in case vitest is a test runner, also make a test rule for it
def vitest_test(**kwargs):
    nodejs_test(
      entry_point = { "@npm//:node_modules/vitest": "vitest.mjs" },
      data = ["@npm//vitest:vitest"] + kwargs.pop("data", []),
      **kwargs
    )
```

I use [Vite](https://vitejs.dev/) as well in my repo to build sites, run local
dev sites with Vue v3, serve prod-like sites for e2e tests, etc. and I don't have
any problem with Vite itself, only Vitest.

It does not seem like the contents of bazel's `node_modules/vitest` directory,
nor the path cause this problem.

---

Testing if symlinking `vitest/` causes problems:

```sh
mv node_modules/vitest/ node_modules/static_vitest/
ln -s $PWD/bazel-out/k8-fastbuild/bin/external/yarn_npm/node_modules/vitest node_modules/
yarn run test:unit run --config=$PWD/static_vite.config.ts static_import.test.ts # passes

# REVERSE
rm node_modules/vitest # no ending '/' we are deleting a symlink
mv node_modules/static_vitest/ node_modules/vitest/
yarn run test:unit run --config=$PWD/static_vite.config.ts static_import.test.ts # passes
```

---

Testing if the bazel cache path causes problems with package resolution in this case"

```sh
HASH="$(echo -n $PWD | md5sum | awk '{ print $1 }')"
mv node_modules/vitest/ node_modules/static_vitest/
ln -s $HOME/.cache/bazel/_bazel_root/$HASH/execroot/demo/bazel-out/k8-fastbuild/bin/external/yarn_npm/node_modules/vitest/ node_modules/
yarn run test:unit run --config=$PWD/static_vite.config.ts static_import.test.ts # passes

# REVERSE
rm node_modules/vitest # no ending '/' we are deleting a symlink
mv node_modules/static_vitest/ node_modules/vitest/
yarn run test:unit run --config=$PWD/static_vite.config.ts static_import.test.ts # passes
```
