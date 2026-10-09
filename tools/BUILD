# *******************************************************************************
# Copyright (c) 2026 Contributors to the Eclipse Foundation
#
# See the NOTICE file(s) distributed with this work for additional
# information regarding copyright ownership.
#
# This program and the accompanying materials are made available under the
# terms of the Apache License Version 2.0 which is available at
# https://www.apache.org/licenses/LICENSE-2.0
#
# SPDX-License-Identifier: Apache-2.0
# *******************************************************************************

load("@aspect_rules_py//py:defs.bzl", "py_venv")
load("@pip_score_coverage_dev//:requirements.bzl", "requirement")
load("@rules_python//python:defs.bzl", "py_binary", "py_test")
load("@rules_python//python:pip.bzl", "compile_pip_requirements")
load("@score_tooling//:defs.bzl", "copyright_checker", "dash_license_checker")
load("@score_tooling//third_party/format:macros.bzl", "use_format_targets")

# Repository hygiene (copyright headers, formatting). Kept out of the root
# package on purpose: these load from dev dependencies, which consumers of
# @score_coverage do not have.

copyright_checker(
    name = "copyright",
    srcs = [
        ".bazelrc",
        ".github",
        "BUILD",
        "MODULE.bazel",
        "README.md",
        "defs.bzl",
        "docs",
        "integration_tests",
        "pyproject.toml",
        "score_coverage",
        "tools",
    ],
    config = "@score_tooling//cr_checker/resources:config",
    template = "@score_tooling//cr_checker/resources:templates",
    visibility = ["//visibility:public"],
)

# C++ and Rust exist only as fixtures inside the nested integration_tests
# workspace, which has its own toolchains; the main module holds Python,
# Starlark and YAML.
use_format_targets(languages = [
    "python",
    "starlark",
    "yaml",
])

compile_pip_requirements(
    name = "requirements_3_12",
    src = "requirements.in",
    python_version = "3.12",
    requirements_txt = "requirements_3_12.txt",
    tags = ["manual"],
)

py_venv(
    name = "ide_support",
    tags = ["manual"],
    venv_name = ".venv_coverage",
    deps = [
        "//score_coverage:coverage_summary_lib",
        "//score_coverage:effective_coverage_lib",
        "//score_coverage:gcov_reporter_lib",
        "//score_coverage:generate_coverage_html_lib",
        "//score_coverage:merger_lib",
        "@score_tools//score_pytest:attribute_plugin",
        requirement("basedpyright"),
    ],
)

# Structural coverage of this repository's own Python (coverage.py via
# `bazel coverage --combined_report=lcov //score_coverage/tests:all`).
py_binary(
    name = "self_coverage_gate",
    srcs = ["self_coverage_gate.py"],
)

py_test(
    name = "self_coverage_gate_test",
    srcs = [
        "self_coverage_gate.py",
        "self_coverage_gate_test.py",
    ],
    imports = ["."],
    main = "self_coverage_gate_test.py",
)

# Eclipse Dash license check over the Python dependencies (cicd-workflows
# license-check runs `bazel run //:license-check`; the root BUILD aliases here).
dash_license_checker(
    src = "//score_coverage:requirements_3_12.txt",
    file_type = "requirements",
    visibility = ["//visibility:public"],
)
