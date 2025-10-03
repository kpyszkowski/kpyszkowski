# Visual Studio Code configuration

## Version
The extension used to customize the UI is deprecated. It means it doesn't work with latest VSC. It needs to be downgraded:
```sh
brew install --cask visual-studio-code@1.93  
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