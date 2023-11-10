1. Install nix
2. `mkdir $HOME/.dotfiles/`
3. `ln -s <my-nix-file> $HOME/.dotfiles/env.nix`
4. Install all packages in nix file:
`nix-env -irf <my-nix-file>`
5. copy nix-diff and nix-update-nixpkgs to somewhere on $PATH
