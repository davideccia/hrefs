<div align="center">

<img src="./www/favicon.svg" alt="" width="96" height="96">

# hrefs

A static start page for the services running on your machine (Docker or not): a list of links, grouped in sections.
No build, no dependencies: one `index.html` and one JS config file.

[Features](#features) · [Usage](#usage) · [Configuration](#configuration) · [Docker](#run-with-docker)

</div>

## Features

- Services grouped into sections, each with a header and rows showing icon, name and host
- Customizable favicon, with the bundled one as fallback
- Single config file (`services.js`), no need to touch the HTML
- Falls back to the first letter of the name when an icon is missing or fails to load
- Empty state with instructions when the list is empty, and a dedicated message if `services.js` is missing or invalid
- Automatic light and dark theme, responsive layout, `prefers-reduced-motion` support
- Works by opening the file directly or from any static web server

## Usage

1. Copy the example file:

   ```sh
   cp www/services.js.example www/services.js
   ```

2. Edit `www/services.js` with your services:

   ```js
   window.SERVICES = [
     {
       title: "Infrastructure",
       services: [
         {
           name: "Dockhand",
           url: "http://localhost:7010",
           icon: "https://cdn.jsdelivr.net/gh/homarr-labs/dashboard-icons/webp/dockhand.webp",
         },
       ],
     },
   ];
   ```

3. Open `www/index.html` in your browser, or serve the folder:

   ```sh
   python3 -m http.server 8000 --directory www
   ```

Reload the page after each change to `services.js`.

> [!NOTE]
> `services.js` is listed in `.gitignore`: your configuration stays local, and only `www/services.js.example` is in the repository.

## Configuration

`window.SERVICES` is an array of sections. Each section has a `title` and a `services` array.

| Field  | Required | Description                                                          |
| ------ | -------- | -------------------------------------------------------------------- |
| `name` | yes      | Name shown in the row                                                |
| `url`  | yes      | Full URL of the service (the displayed host is derived from it)      |
| `icon` | no       | URL of a square image (24x24 px). If missing, the name's initial is shown |

To use your own favicon, set `window.FAVICON` in `services.js` to a URL or a path relative to `index.html` (for example `custom_favicon.png` placed in `www/`, which is git-ignored (any `custom_favicon.*` is)). If it is missing or fails to load, the bundled `favicon.svg` is used.

```js
window.FAVICON = "./custom_favicon.png";
```

A flat array of services, without sections, still works and is rendered without a header.

> [!TIP]
> You can use [Dashboard Icons](https://github.com/homarr-labs/dashboard-icons) through jsDelivr for the icons.

## Run with Docker

The repository includes a `compose.yml` that serves `www/` with the [linuxserver/nginx](https://docs.linuxserver.io/images/docker-nginx/) image:

```sh
cp .env.example .env   # optional, defaults apply to empty values
docker compose up -d
```

Then open <http://localhost>. Settings are read from `.env`:

| Variable                  | Default    | Description                 |
| ------------------------- | ---------- | --------------------------- |
| `HREFS_NGINX_PUID`        | `1000`     | User ID the container runs as  |
| `HREFS_NGINX_PGID`        | `1000`     | Group ID the container runs as |
| `HREFS_NGINX_TZ`          | `Etc/UTC`  | Container timezone          |
| `HREFS_NGINX_HTTP_PORT`   | `80`       | Host port mapped to HTTP    |
| `HREFS_NGINX_HTTPS_PORT`  | `443`      | Host port mapped to HTTPS   |

> [!TIP]
> Empty values in `.env` fall back to the defaults, so you only need to fill in what you want to change.

`www/` is mounted as a volume, so edits to `services.js` on the host show up on reload.

## Structure

```text
.
├── compose.yml               # nginx container serving www/
├── .env.example              # Docker settings template
└── www/
    ├── index.html            # page and rendering logic
    ├── services.js           # your configuration (ignored by git)
    ├── services.js.example   # template to copy
    └── favicon.svg
```
