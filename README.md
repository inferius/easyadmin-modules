# EasyAdmin moduly

Registr modulů pro administraci [EasyAdmin](https://github.com/inferius/EasyAdmin) a projekty na stejném jádru
(Atoda, cestadocloudu). Administrace čte `index.json` z tohoto repozitáře a moduly instaluje na stránce
**Moduly -> Dostupné online**: stáhne zip z Releases, ověří otisk sha256, rozbalí ho do `modules/<id>` a před migracemi
modulu zálohuje databázi.

## Moduly

| Modul | Verze | Popis |
|---|---|---|
| <a id="instagram"></a>`instagram` | 1.1.0 | Poslední fotky z Instagramu firmy na webu - připojení tlačítkem s návodem, obnova tokenu a stahování fotek samo (plánovaná úloha), upozornění e-mailem. Web: `instagramMedia($limit)`. |

## Nastavení administrace

`config.inc.php`:

```php
"modules" => [
    "registries" => [
        [ "url" => "https://raw.githubusercontent.com/inferius/easyadmin-modules/main/index.json" ],
    ],
],
```

Web projektu načítá moduly ze složky administrace: `$config["path"]["absolute"]["modules"]` (např. `ROOT_ABSOLUTE_DIR . "/../admin/modules"`).
Plánované úlohy modulů běží po návštěvě stránek, pojistka pro plánovač hostingu: `php tools/cron.php`.

## Vydání modulu

Formát modulu a manifestu: `_project/module_example/hello/README.md` v repozitáři EasyAdmin.

1. Ve zdroji modulu zvýšit `version` v `module.php`, přeložit styly a skripty (`node build-tools/build.mjs --dir=modules/<id>`).
2. Balíček: `php tools/module_pack.php modules/<id> --out=dist --url="https://github.com/inferius/easyadmin-modules/releases/download/<id>-v<verze>/{file}"`
3. Release s tagem `<id>-v<verze>` a zipem: `gh release create <id>-v<verze> dist/<id>-<verze>.zip --repo inferius/easyadmin-modules --title "<Název> <verze>" --notes "..."`
4. Záznam verze, který vypsal `module_pack` (verze, `core`, `url`, `sha256`), přidat do `index.json` k modulu (starší verze nechat - lze se k nim vrátit) a pushnout.

Administrace bere nejnovější verzi kompatibilní s verzí jádra (`core`). Soukromé moduly patří do vlastního
soukromého registru (stejný formát, v configu k registru `"token"` - GitHub token jen pro čtení).
