# isvctl-provider-example

> **Example only - not a real provider.** Every script here is an unimplemented
> scaffold: it returns dummy success under `ISVCTL_DEMO_MODE=1` and
> "Not implemented" otherwise. A passing run says nothing about any platform.
> This repository is not an NVIDIA-supported product.

A living example of an externally maintained provider for the
[NVIDIA AI Cloud Validation Suite](https://github.com/NVIDIA/ai-cloud-validation).
It shows the shape a partner repository takes: the provider's `config/` and
`scripts/` live here, while the validation suite supplies the CLI, the suites,
and the validation code.

It was generated with `isvctl provider scaffold example` from ai-cloud-validation
commit `1ad45f2`, and passes against release 0.13.0. The only change from the
generated scaffold is that the `.scaffold-meta` marker was removed: it exists
so `isvctl provider scaffold --overwrite` knows it may delete the directory,
which in a git repository would include `.git`.

## Reproducing the results

This provider implements nothing, so it only runs in demo mode
(`ISVCTL_DEMO_MODE=1`); without it each script fails with "Not implemented".

From an ai-cloud-validation checkout with the provider registry
(`isvctl provider fetch`), fetch it by name and run it like any provider. Its
registry entry has `status: demo`, so isvctl turns demo mode on and never uploads
the dummy results:

```bash
uv run isvctl provider fetch example
uv run isvctl test run --provider example --suite vm
```

Release 0.13.0 predates the registry: clone this repository next to the
checkout and pass the config path instead, from the checkout root. Here demo
mode must be set by hand, and `--no-upload` keeps the dummy results out of the
ISV Lab Service:

```bash
git clone https://github.com/NVIDIA/ai-cloud-validation.git
cd ai-cloud-validation
git checkout v0.13.0
uv sync
git clone https://github.com/abegnoche/isvctl-provider-example.git ../isvctl-provider-example

ISVCTL_DEMO_MODE=1 uv run isvctl test run --no-upload -f ../isvctl-provider-example/config/vm.yaml
```

Some suites need a capability, mirroring the suite's `make demo-test`:

| Suite | Extra flags |
| --- | --- |
| `bare_metal`, `control-plane`, `iam`, `vm` | none |
| `network`, `observability`, `security` | `--capability vm` |
| `image-registry` | once with `--capability vm`, once with `--capability bare_metal` |

## Layout

- `config/` - YAML wiring: each file imports a suite and points its steps at `scripts/`.
- `scripts/` - the scaffold scripts, one directory per domain. See
  [`scripts/README.md`](scripts/README.md) for the script contract.

## License

[Apache-2.0](LICENSE)
