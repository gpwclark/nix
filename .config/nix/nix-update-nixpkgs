#!/usr/bin/env nix-shell
#!nix-shell -i bash -p bash curl jq
# shellcheck shell=bash

# Just go with the revision that works for stable darwin. Might as well for now
# unless there's an issue.

set -euo pipefail

dryrun=0

usage() {
  cat <<USAGEEOF
Usage: $0 [OPTIONS]

Options:
  -h  --help      show this help
  -n  --dry-run   do not make any changes
USAGEEOF
}

for arg in "$@"; do
  case ${arg} in
    '-n' | '--dry-run')
      dryrun=1
      ;;
    '-h' | '--help')
      usage
      exit 0
      ;;
    *)
      echo "$0: Unexpected argument: ${arg}" >&2
      usage >&2
      exit 1
      ;;
  esac
done


revision=$(curl --silent --show-error 'https://monitoring.nixos.org/prometheus/api/v1/query?query=channel_revision' \
  | jq -r '.data.result[]|select(.metric.status == "stable" and .metric.variant == "darwin").metric.revision')
nixexpr="import (fetchTarball \"https://github.com/NixOS/nixpkgs/archive/${revision}.tar.gz\")"
nixpkgsfile=~/.nix-defexpr/nixpkgs/default.nix

mkdir -p "$(dirname "${nixpkgsfile}")"
if [[ -f "${nixpkgsfile}" ]] && diff -q "${nixpkgsfile}" - <<< "${nixexpr}" >/dev/null; then
  echo 'nixpkgs already up to date'
  exit 0
fi

if [[ ${dryrun} -ne 0 ]]; then
  echo "This would set nixpkgs to revision ${revision}"
else
  echo "Setting nixpkgs to revision ${revision}"
  echo "${nixexpr}" >${nixpkgsfile}
fi

"$(dirname "$0")/nix-diff" "${revision}"
# vim: set sw=2 sts=2 ts=8 et ft=bash:
