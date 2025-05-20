# TechBits -- 技术札记

## NixOS

### [`<pkg>.overrideAttrs`](https://nixos.org/manual/nixpkgs/stable/#sec-pkg-overrideAttrs)

The function overrideAttrs allows overriding the attribute set passed to a stdenv.mkDerivation call, producing a new derivation based on the original one. This function is available on all derivations produced by the stdenv.mkDerivation function, which is most packages in the nixpkgs expression pkgs.

Example usages:

```nix
{
  helloBar = pkgs.hello.overrideAttrs (
    finalAttrs: previousAttrs: {
      pname = previousAttrs.pname + "-bar";
    }
  );
}
```

In the above example, “-bar” is appended to the pname attribute, while all other attributes will be retained from the original hello package.

The argument previousAttrs is conventionally used to refer to the attr set originally passed to stdenv.mkDerivation.

The argument finalAttrs refers to the final attributes passed to mkDerivation, plus the finalPackage attribute which is equal to the result of mkDerivation or subsequent overrideAttrs calls.

If only a one-argument function is written, the argument has the meaning of previousAttrs.

Function arguments can be omitted entirely if there is no need to access previousAttrs or finalAttrs.

```nix
{
  helloWithDebug = pkgs.hello.overrideAttrs {
    separateDebugInfo = true;
  };
}
```

In the above example, the separateDebugInfo attribute is overridden to be true, thus building debug info for helloWithDebug.
Note

> Note that separateDebugInfo is processed only by the stdenv.mkDerivation function, not the generated, raw Nix derivation. Thus, using overrideDerivation will not work in this case, as it overrides only the attributes of the final derivation. It is for this reason that overrideAttrs should be preferred in (almost) all cases to overrideDerivation, i.e. to allow using stdenv.mkDerivation to process input arguments, as well as the fact that it is easier to use (you can use the same attribute names you see in your Nix code, instead of the ones generated (e.g. buildInputs vs nativeBuildInputs), and it involves less typing).
