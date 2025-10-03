# Visual Studio Code configuration

## Version
The extension used to customize the UI is deprecated. It means it doesn't work with latest VSC. It needs to be downgraded.
For Homebrew to install old version of application it needs to be in a tap. Local tap needs to be created:

```sh
brew tap-new kpyszkowski/local
mkdir -p $(brew --repo kpyszkowski/local)/Casks
curl -o $(brew --repo kpyszkowski/local)/Casks https://raw.githubusercontent.com/Homebrew/homebrew-cask/b8fe6eb/Casks/v/visual-studio-code.rb
brew install --cask kpyszkowski/local/visual-studio-code
```
To prevent it being automatically updated, update mode setting must me changed:
```json
{
    "update.mode": "manual"
}
```

> [!tip]
>
> It is already included in the [settings file](settings.jsonc).

## Extensions
To install extensions run the following command from the repo root directory:
```sh
cat ./configs/vscode/extensions.txt | xargs -n 1 code --install-extension
```

## Settings
Copy [settings file](settings.jsonc) contents to VSC local settings file

> [!tip]
>
> To open the local settings file double tap Shift and type `> user settings json`

## Fonts
Settings include Fira Code as the UI and Editor font:
```sh
brew install --cask font-fira-code
```