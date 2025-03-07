# Tauri + React + Typescript

![image](https://github.com/user-attachments/assets/f122f307-785d-4335-87e7-6979b3362bf3)

How to make this:

Get node setup and rust and pnpm.

```
pnpm create tauri-tray-app
```

I used React, Typescript, and pnpm. Once created, I went into the directory and ran:


```
pnpm install
```

Then I changed the Cargo.toml to include the tray feature:

```
[package]
name = "tray-example"
version = "0.1.0"
description = "A Tauri App"
authors = ["you"]
features = [ "tray-icon" ]
edition = "2021"
```

You also need to enable the default app icon capability (in `src-tauri/capabilities/default.json`):

```json
{
  "$schema": "../gen/schemas/desktop-schema.json",
  "identifier": "default",
  "description": "Capability for the main window",
  "windows": ["main"],
  "permissions": [
    "core:default",
    "opener:default",
    "core:app:allow-default-window-icon"
  ]
}
```

In the App.tsx file, I added the following code:

```tsx
  const menuRef = useRef(null as TrayIcon | null | boolean);

  useEffect(() => {
    const createTray = async () => {
      if (menuRef.current) {
        return;
      }
      menuRef.current = true;
      const menu = await Menu.new();
      menu.append(
        await MenuItem.new({
          text: "Hello",
          action: () => console.log("Hello menu click"),
        })
      );
      const options = {
        icon: (await defaultWindowIcon()) as Image,
        menu,
        title: "HI",
        tooltip: "Hello, World!",
        action: () => console.log("Hello tray click"),
      };
      const tray = await TrayIcon.new(options);
      tray.setShowMenuOnLeftClick(true);
      menuRef.current = tray;
    };
    createTray();
  }, []);
```

This is probably the wrong way to this and an abuse of refs. But I didn't want a double useEffect. :


At this point, you can run the app with `pnpm tauri dev`.


# References:

* https://v2.tauri.app/reference/acl/capability/
* https://v2.tauri.app/learn/system-tray/
* https://v2.tauri.app/reference/javascript/api/namespacetray/
* https://github.com/tauri-apps/tauri/issues/8982
