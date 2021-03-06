load("@io_bazel_rules_docker//docker:docker.bzl", "docker_bundle")
load("@io_bazel_rules_docker//contrib:push-all.bzl", "docker_push")
load("@io_bazel_rules_go//go:def.bzl", "go_binary", "go_library")
load("@io_bazel_rules_docker//go:image.bzl", "go_image")

docker_bundle(
    name = "bundle",
    images = {
        "{STABLE_DOCKER_REPO}/issue-creator:{DOCKER_TAG}": ":issue-creator-image",
        "{STABLE_DOCKER_REPO}/issue-creator:latest": ":issue-creator-image",
        "{STABLE_DOCKER_REPO}/issue-creator:latest-{BUILD_USER}": ":issue-creator-image",
    },
    stamp = True,
)

docker_push(
    name = "push",
    bundle = ":bundle",
)

go_library(
    name = "go_default_library",
    srcs = ["main.go"],
    importpath = "k8s.io/test-infra/robots/issue-creator",
    visibility = ["//visibility:private"],
    deps = [
        "//robots/issue-creator/creator:go_default_library",
        "//robots/issue-creator/sources:go_default_library",
    ],
)

go_image(
    name = "issue-creator-image",
    base = "@distroless-base//image",
    embed = [":go_default_library"],
    importpath = "k8s.io/test-infra/robots/issue-creator",
    visibility = ["//visibility:public"],
)

filegroup(
    name = "package-srcs",
    srcs = glob(["**"]),
    visibility = ["//visibility:private"],
)

filegroup(
    name = "all-srcs",
    srcs = [
        ":package-srcs",
        "//robots/issue-creator/creator:all-srcs",
        "//robots/issue-creator/sources:all-srcs",
        "//robots/issue-creator/testowner:all-srcs",
    ],
    visibility = ["//visibility:public"],
)

filegroup(
    name = "test-owners-csv",
    srcs = [
        "test_owners.csv",
    ],
)

# Delete after gazelle stops forcing its creation
go_binary(
    name = "issue-creator",
    embed = [":go_default_library"],
    importpath = "k8s.io/test-infra/robots/issue-creator",
    visibility = ["//visibility:public"],
)
