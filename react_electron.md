# Desktop App with React and Electron

I will show you how to generate a desktop app with `React` and `Electron`.

### Steps

1. Creating a `React` project:

```
npx create-react-app my-app
```

2. Install the following packages:

```
npm install electron --save-dev
npm install wait-on --save-dev
npm install cross-env
npm install electron-packager
```

3. Preparing the project to use `Electron`

Create a folder called `electron` inside of the `public` folder and add the `main.js` file:

```text
.
└── my-app
    └── public
        └── electron
            └── main.js
```

The `main.js` should looks like this:

```javascript
const { app, BrowserWindow } = require('electron');
const path = require("path");

const createMainWindow = () => {
    const mainWindow = new BrowserWindow({
        width: 800,
        height: 600,
    });

    // Load index.html from public/index.html
    if (app.isPackaged) {
        mainWindow.loadFile(path.join(__dirname, '../index.html'));
        return;    
    }
    
    mainWindow.loadURL('http://localhost:3000');
};

// This method will be called when Electron has finished
// initialization and is ready to create browser windows.
// Some APIs can only be used after this event occurs.
app.whenReady().then(() => {
    createMainWindow();
  
    app.on('activate', () => {
      // On macOS it's common to re-create a window in the app when the
      // dock icon is clicked and there are no other windows open.
      if (BrowserWindow.getAllWindows().length === 0) {
          createMainWindow();
      }
    })
});
  
// Quit when all windows are closed, except on macOS. There, it's common
// for applications and their menu bar to stay active until the user quits
// explicitly with Cmd + Q.
app.on('window-all-closed', () => {
    if (process.platform !== 'darwin') {
        app.quit();
    } 
});
```
Click [here](https://www.electronjs.org/pt/docs/latest/tutorial/quick-start) to get more information about this piece of code.


4. Add `main` and `homepage` property to your `package.json` file:

```json
{
    "name": "my-app",
    "version": "0.1.0",
    "main": "public/electron/main.js",
    "homepage": "."
}
```

5. Add `electron` and `desktop-dev` script to your `package.json` to run as a desktop app:

```json
{
    "scripts": {
        "start": "react-scripts start",
        "build": "react-scripts build",
        "test": "react-scripts test",
        "eject": "react-scripts eject",
        "electron": "wait-on tcp:3000 && electron .",
        "desktop-dev": "concurrently \"cross-env BROWSER=none react-scripts start\" \"npm:electron\""
      },
}
```

6. Running as desktop app:

```
npm run desktop-dev
```

7. Generating desktop app

We already prepared our project to run as a desktop app. Now we will add configs to generate a package.
Add `package.json` file to the `public` folder:

```
cd public
npm run init
```

Update the `main` property in `package.json` from `public` folder:
```json
"main": "electron/main.js"
```

Add `homepage` property to `package.json` from `public` folder: 

```json
"homepage": "."
```

8. Add script `package` to the main `package.json`:
```json
"scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject",
    "desktop-dev": "concurrently \"cross-env BROWSER=none react-scripts start\" \"npm:electron\"",
    "electron": "wait-on tcp:3000 && electron .",
    "package": "npm run build && electron-packager ./build --platform=win32 --arch=x64 --icon=./public/icon"
},
```

And finally, to generate the desktop app you just need to run the following command:

```
npm run package
```

