# nix-cargo-integration

Library to easily and effortlessly integrate Cargo projects with Nix.

- Uses [dream2nix] to build Cargo packages and [devshell] to provide a development shell.
- Allows configuration from `Cargo.toml` file(s) via `package.metadata.nix`
and `workspace.metadata.nix` attributes.
- Has sensible defaults, and strives to be compatible with Cargo (autobins, etc.).
- Aims to offload work from the user; comes with useful configuration options
(like `renameOutputs`, `defaultOutputs` etc.)
- Can generate nixpkgs-compatible Nix expressions that captures all your packages
dependencies / env vars and so on.
You don't need to maintain a seperate derivation for nixpkgs!
(see `Generating a nixpkgs-compatible package expression` in manual)
- A CLI tool that let's you compile and run arbitrary Rust repositories directly
without messing with any files or setting up overlays (see `Using the nci CLI` in manual)

Check out [rust-nix-templater](https://github.com/yusdacra/rust-nix-templater)
if you want an easy way to generate nix-specific boilerplate for your projects!

## Usage

### With flakes

Run `nix flake init -t github:yusdacra/nix-cargo-integration`.

Or add:
```nix
{
  inputs = {
    nci.url = "github:yusdacra/nix-cargo-integration";
  };
  outputs = inputs: inputs.nci.lib.makeOutputs { root = ./.; };
}
```
to your `flake.nix`.

### Without flakes

You can use [flake-compat] to provide the default outputs of the flake for non-flake users.

If you aren't using flakes, you can do (in your `default.nix` file for example):
```nix
let
  nciSrc = fetchTarball {
    url = "https://github.com/yusdacra/nix-cargo-integration/archive/<rev>.tar.gz";
    sha256 = "<hash>";
  };
  nci = import nciSrc;
in nci.makeOutputs { root = ./.; }
```

You can also couple it with [niv](https://github.com/nmattia/niv):
- First run `niv add yusdacra/nix-cargo-integration`
- Then you can write in your `default.nix` file:
    ```nix
    let
      sources = import ./sources.nix;
      nci = import sources.nix-cargo-integration;
    in nci.makeOutputs { root = ./.; }
    ```

[devshell]: https://github.com/numtide/devshell "devshell"
[flake-compat]: https://github.com/edolstra/flake-compat "flake-compat"
[dream2nix]: https://github.com/nix-community/dream2nix "dream2nix"