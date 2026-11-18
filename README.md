# Silo widget for Dynacat

A [Dynacat](https://github.com/Panonim/dynacat) widget for a
[Silo](https://github.com/Silo-Server/silo-server) media server: live streams,
streaming throughput and recently added titles.

![Silo widget, small column](widget/images/preview.png)

- Summary: active streams, transcodes, the combined bitrate of the active
  streams, and the data served in the last 6 hours.
- Throughput chart: viewer egress over the last 6 hours, with the peak.
- One row per stream: title, user and profile, client, bitrate, play method
  (Direct Play, Direct Stream, Remux or Transcode) and progress.
- Recently added: the 8 newest movies and series, with posters, linking to the
  title in Silo.

![Silo widget, full column](widget/images/preview-full.png)

## Use it

The widget reads three Silo routes with one API key. Set these in Dynacat's
environment:

| Variable | Description |
|----------|-------------|
| `SILO_URL` | Address Dynacat reaches Silo on, without a trailing slash, e.g. `http://silo:8080` |
| `SILO_API_KEY` | An API key from an admin account, scoped to `admin:monitor:read` and `catalog:read` |
| `SILO_PROFILE_ID` | The profile whose libraries recently added shows |

[widget/widget.md](widget/widget.md) explains how to create the key and find
the profile ID.

Then add the widget to `dynacat.yml` in one of two ways.

### As a custom-api widget

Copy [custom-api.yml](custom-api.yml) into a column's `widgets:` list. It is
the template in `widget/template.txt` with its requests written out, and works
on any Dynacat version with `custom-api` subrequests.

### From the Dynawidgets catalog

Once the widget is in the
[Dynawidgets repository](https://github.com/Panonim/dynawidgets):

```yaml
- type: dynawidgets
  widget: silo
  headers:
    Authorization: Bearer ${SILO_API_KEY}
  cache: 30s
```

## What it reads

| Request | Silo route | Scope |
|---------|------------|-------|
| Streams | `GET /api/v2/admin/sessions` | `admin:monitor:read` |
| Throughput | `GET /api/v2/admin/stats/timeseries?hours=6` | `admin:monitor:read` |
| Recently added | `GET /api/v2/catalog?sort=-added_at&type=video&limit=8` | `catalog:read` |

The key cannot play media, control sessions, change settings, manage users or
read file paths. Dynacat fetches the posters and serves them from its own
cache, so the browser does not contact Silo.

## Files

- `widget/`: the widget in the Dynawidgets layout (`template.txt`,
  `meta.yml`, `widget.md`, `images/`).
- `custom-api.yml`: the same widget as a `custom-api` config. A change to
  `widget/template.txt` is made here too.

## Tested

Rendered by Dynacat built from source (Dynacat `main`, 2026-11-18) in a small
and a full column, against a stand-in for Silo that answers the three routes
in the shapes of Silo's `/api/v2` OpenAPI contract. Not yet run against a live
Silo server.

## License

[MIT](LICENSE)
