load("@build_bazel_rules_nodejs//:index.bzl", "nodejs_test")
load("@yarn_npm//vitest:index.bzl", "vitest_test")

vitest_test(
    name = "global_unit_test",
    args = [
        "run",
        "--config=$(execpath :global_vite.config.ts)",
    ],
    data = [
        ":global_import.test.ts",
        ":global_vite.config.ts",
        "@yarn_npm//@types/jsdom",
        "@yarn_npm//@types/node",
        "@yarn_npm//@vitejs/plugin-vue",
        "@yarn_npm//@vitejs/plugin-vue-jsx",
        "@yarn_npm//@vue/test-utils",
        "@yarn_npm//vite",
        "@yarn_npm//vue",
    ],
)

vitest_test(
    name = "static_unit_test",
    args = [
        "run",
        "--config=$(execpath :static_vite.config.ts)",
    ],
    data = [
        ":static_import.test.ts",
        ":static_vite.config.ts",
        "@yarn_npm//@types/jsdom",
        "@yarn_npm//@types/node",
        "@yarn_npm//@vitejs/plugin-vue",
        "@yarn_npm//@vitejs/plugin-vue-jsx",
        "@yarn_npm//@vue/test-utils",
        "@yarn_npm//vite",
        "@yarn_npm//vue",
    ],
)

# Note, experiencing the same problem using npm toolchain instead of yarn.
nodejs_test(
    name = "static_unit_test_npm",
    args = [
        "run",
        "--config=$(execpath :static_vite.config.ts)",
    ],
    data = [
        ":static_import.test.ts",
        ":static_vite.config.ts",
        "@npm//@vitejs/plugin-vue",
        "@npm//@vitejs/plugin-vue-jsx",
        "@npm//vite",
        "@npm//vitest",
    ],
    entry_point = {"@npm//:node_modules/vitest": "vitest.mjs"},
)
