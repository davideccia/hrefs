<div align="center">

<img src="./favicon.svg" alt="" width="96" height="96">

# docker@local

A static start page for the Docker services running on your machine.
No build, no dependencies: one `index.html` and one JS config file.

[Features](#features) · [Usage](#usage) · [Configuration](#configuration) · [Docker](#serve-it-with-docker)

</div>

## Features

- Services grouped into sections, each with a header and rows showing icon, name and host
- Single config file (`services.js`), no need to touch the HTML
- Falls back to the first letter of the name when an icon is missing or fails to load
- Empty state with instructions when the list is empty, and a dedicated message if `services.js` is missing or invalid
- Automatic light and dark theme, responsive layout, `prefers-reduced-motion` support
- Works by opening the file directly or from any static web server

## Usage

1. Copy the example file:

   ```sh
   cp services.js.example services.js
   ```

2. Edit `services.js` with your services:

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

3. Open `index.html` in your browser, or serve the folder:

   ```sh
   python3 -m http.server 8000
   ```

Reload the page after each change to `services.js`.

> [!NOTE]
> `services.js` is listed in `.gitignore`: your configuration stays local, and only `services.js.example` is in the repository.

## Configuration

`window.SERVICES` is an array of sections. Each section has a `title` and a `services` array.

| Field  | Required | Description                                                          |
| ------ | -------- | -------------------------------------------------------------------- |
| `name` | yes      | Name shown in the row                                                |
| `url`  | yes      | Full URL of the service (the displayed host is derived from it)      |
| `icon` | no       | URL of a square image (24x24 px). If missing, the name's initial is shown |

A flat array of services, without sections, still works and is rendered without a header.

> [!TIP]
> You can use [Dashboard Icons](https://github.com/homarr-labs/dashboard-icons) through jsDelivr for the icons.

## Serve it with Docker

To serve the page from a container, mount the folder read-only on a static web server such as nginx:

```yaml
services:
  html:
    image: nginx:alpine
    container_name: html
    restart: unless-stopped
    ports:
      - 8080:80
    volumes:
      - ./:/usr/share/nginx/html:ro
```

With the volume mounted, you can edit `services.js` directly on the host.

## Structure

```text
.
├── index.html            # page and rendering logic
├── services.js           # your configuration (ignored by git)
├── services.js.example   # template to copy
└── favicon.svg
```
