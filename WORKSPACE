workspace(name = "demo")

load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive")

# NodeJS Toolchains
http_archive(
    name = "build_bazel_rules_nodejs",
    sha256 = "c78216f5be5d451a42275b0b7dc809fb9347e2b04a68f68bad620a2b01f5c774",
    urls = ["https://github.com/bazelbuild/rules_nodejs/releases/download/5.5.2/rules_nodejs-5.5.2.tar.gz"],
)


load("@build_bazel_rules_nodejs//:repositories.bzl", "build_bazel_rules_nodejs_dependencies")

build_bazel_rules_nodejs_dependencies()

load("@build_bazel_rules_nodejs//:index.bzl", "node_repositories", "npm_install", "yarn_install")

node_repositories(
    node_version = "16.15.1",
)

npm_install(
    name = "npm",
    package_json = "//:package.json",
    package_lock_json = "//:package-lock.json",
    package_path = "/",
)

yarn_install(
    name = "yarn_npm",
    package_json = "//:package.json",
    yarn_lock = "//:yarn.lock",
    package_path = "/",
)
