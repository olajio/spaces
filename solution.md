Looking at both spaces carefully, the data views are actually very similar — the `front-end-dev` space has plenty of APM-related data views including the critical static APM data view (`*:traces-apm*,*:apm-*,*:logs-apm*,*:apm-*,*:metrics-apm*,*:apm-*` named "APM").

However, there's one key difference. The `development` space has a **static APM data view with a specific ID convention**:

**`development` space:**
- ID: `apm_static_data_view_id_development`
- Title: `*:traces-apm*,*:apm-*,*:logs-apm*,*:apm-*,*:metrics-apm*,*:apm-*`
- Name: `APM`

**`front-end-dev` space:**
- ID: `473a20db-07e2-4d65-8dbc-457feb4d2328` (random UUID)
- Title: `*:traces-apm*,*:apm-*,*:logs-apm*,*:apm-*,*:metrics-apm*,*:apm-*`
- Name: `APM`

(There's also a second one with ID `b36d9c69-aa1b-46f8-bca6-0029a91f6a10` with the same title.)

The Observability APM app doesn't just look for *any* data view with APM indices — it looks for the data view with the **specific ID** `apm_static_data_view_id_<space_name>`. In the `development` space, that ID is `apm_static_data_view_id_development`, which follows the convention the APM app expects. In the `front-end-dev` space, there's no data view with the ID `apm_static_data_view_id_front-end-dev`.

**The fix:** Create a data view in the `front-end-dev` space with the specific ID `apm_static_data_view_id_front-end-dev`. You can do this via Dev Tools in Kibana:

```
POST kbn:/s/front-end-dev/api/data_views/data_view
{
  "data_view": {
    "id": "apm_static_data_view_id_front-end-dev",
    "title": "*:traces-apm*,*:apm-*,*:logs-apm*,*:apm-*,*:metrics-apm*,*:apm-*",
    "name": "APM",
    "timeFieldName": "@timestamp"
  }
}
```

Alternatively, you can navigate to the APM app in the `front-end-dev` space as an admin/superuser first — Kibana's APM app typically auto-creates this static data view on first access by a user with sufficient privileges. The user with `feature_apm.minimal_all` might not have enough privilege to trigger the auto-creation, but an admin visiting APM in that space should trigger it.
