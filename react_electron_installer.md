# Create an Installer with Electron

I will use `React` and `Electron` to create the installer

1. Creating a `React` project:

```
npx create-react-app my-app
```

2. Install the following packages:

```
npm install electron --save-dev
npm install electron-builder --save-dev
```

3. Preparing the project to use `Electron`

Create a folder called `electron` inside of the `public` folder and add the `electron.js` file:

```text
.
└── my-app
    └── public
        └── electron.js
```

The `electron.js` should looks like this:

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
        mainWindow.loadFile(path.join(__dirname, 'index.html'));
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
    "main": "electron.js",
    "homepage": "."
}
```

5. Add `build` property to your `package.json` file:

```json
"build": {
    "appId": "my-installer",
    "productName": "my-installer",
    "win": {
      "target": [
        "nsis"
      ]
    },
    "nsis": {
      "artifactName": "my-installer.exe",
      "createDesktopShortcut": true,
      "oneClick": false,
      "perMachine": true,
      "allowToChangeInstallationDirectory": true
    }
},
```

Click [here](https://www.electron.build/) to get more information about this piece of code.

6. Add `package-installer` script to your `package.json` file:

```json
{
    "scripts": {
        "start": "react-scripts start",
        "build": "react-scripts build",
        "test": "react-scripts test",
        "eject": "react-scripts eject",
        "package-installer": "npm run build && electron-builder"
    },
}
```

7. Generating installer: 

Run the following command to generate the installer

```
npm run package-installer
```