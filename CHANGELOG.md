# Changelog

## v0.0.8

* Fix build when vdso build is triggered

## v0.0.7

* Only copy directories if they exist.

## v0.0.6

* Add ability to cross-compile x86 to x86.

## v0.0.5

* Add parameter to allow use of custom cross-compiler when building `scripts/`
  binaries.

## v0.0.4

* Fix bug whereby if the `prefix` env var was set in the host it'd cause
  weirdness in the scripts/ bins dance.

## v0.0.3

* Updated script to correctly generate `scripts/` binaries for the _target_
  architecture.

## v0.0.2

* Transition to building without writing to the source directory.

## v0.0.1

First release.
