# Project-Vite-Setup

The purpose of this project is to break down the files and configuration needed to reproduce a devcontainer in which everyone using the same devcontainer JSON configuration file will have the same development environment.

This files in the directory `.devcontainer` were originally inspired from this [repository](https://github.com/johnatag/PodmanDevContainer) made by [johnatag](https://github.com/johnatag) in which he explains how to set everything up in a MacOS environment. The containerization platform used for this is Podman for more security purposes as it does not require root access as opposed to Docker.

This repository was created in order to uniformize everyone of my Vite project. Because, originally, the main and most important feature of Vite, which is Hot Module Replacement, was not working at all and the load time of every single page was indefinite and unpredictable.

# How to Set Project up for Projects with Vite

0. Follow along the configuration steps in [johnatag's repository](https://github.com/johnatag/PodmanDevContainer) before going into the Vite + devcontainer setup. However, replace the content of his Containerfile by the content of the one inside this repository.

```bash
FROM node:bullseye-slim
RUN groupadd -g 1001 vscode \
    && useradd -u 1001 -g vscode -s /bin/bash -m vscode

USER vscode
```

1. Add to devcontainer.json the following line :

```JSON
// creates the vite project after creating the container
"postCreateCommand": "npm create vite@latest",
```

-   You may also add every extension and setting of your liking inside the list of extensions in the devcontainer.json file by going into the extension tab and clicking on the settings cog and clicking on "Add to devcontainer.json"

2. Create devcontainer by pressing `CMD + Shift + P` and by selecting `>Dev Containers: Rebuild and Reopen in Container`

3. After selecting the the framework and the Javascript variant, execute the following line and add it to the devcontainer.json associated to the key value `"postStartCommand"`.

```bash
npm install && npm run dev
```

4. After creating the project, add the following lines inside the `defineConfig()` function inside the `vite.config.ts` file:

```Javascript
//Optional : used for replacing the current absolute path starting from src by @
resolve: {
	alias: [{ find: "@", replacement: path.resolve(__dirname, "src") }],
},

server: {
        //solves the load time issue
	//gives network connectivity to our container and exposing the port to the container
	host: "0.0.0.0",
	strictPort: true,
	port: 5173,
	origin: 'http://localhost:5173',
        //solves the HMR issue
	//stated in vite documentation to communicate with container
	watch: { usePolling: true },
	},
```

-   NB : the vite.config.ts file was not added to the repository because people might fork this repository and create a their Vite project afterwards.
