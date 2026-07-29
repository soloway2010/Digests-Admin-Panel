# Digests Admin Panel (Appsmith)

Appsmith app for viewing digests, editing activity names, and browsing the activity timeline. Built against the Digest.Bot schema (`digests`, `user_activities`, `digest_activities`, `teammates`).

See [Appsmith docs](https://docs.appsmith.com/) for self-hosting and Git sync.

## Admins vs viewers

No Business Edition / GAC required. Admins are a fixed email allowlist checked against [`appsmith.user.email`](https://docs.appsmith.com/reference/appsmith-framework/context-object):

```js
["p.solovev@sdgroup.ai"].map((e) => e.toLowerCase()).includes((appsmith.user.email || "").toLowerCase())
```

| Who | Access |
|-----|--------|
| Email in the allowlist | All digests & timeline activities; Teammates page |
| Everyone else | Only their own digests/activities (email local-part matched to `digests.user_name`) |

### Add or remove admins

Edit the email array in these places (keep the lists identical):

- `pages/Submitted digests/queries/SelectQuery/SelectQuery.txt`
- `pages/Submitted digests/queries/UpdateDigestActivity/UpdateDigestActivity.txt`
- `pages/Activity Timeline/queries/SelectQuery/SelectQuery.txt`
- `pages/Activity Timeline/queries/UpdateQuery/UpdateQuery.txt`
- `pages/Activity Timeline/queries/DeleteQuery/DeleteQuery.txt`
- `pages/Activity Timeline/widgets/Container1/data_table.json` (Delete column visibility)
- `pages/Teammates/queries/{Insert,Update,Delete}Query/*.txt`
- `pages/Teammates/widgets/Container1/Container1.json` (page visibility)

Example with two admins:

```js
["p.solovev@sdgroup.ai", "other@sdgroup.ai"].map((e) => e.toLowerCase()).includes((appsmith.user.email || "").toLowerCase())
```

After editing `.txt` query files, copy the same body into the matching `metadata.json` `unpublishedAction.actionConfiguration.body` (or edit the query in the Appsmith UI and commit).

## Pages

1. **Submitted digests** — list digests; open one to see `digest_activities`; click an activity to rename it.
2. **Activity Timeline** — `user_activities` (name, dates, sighting count); rename/delete.
3. **Teammates** — roster CRUD; visible only to allowlisted admins.

## Identity matching

Viewers are scoped with:

```text
user_name ILIKE '%' || appsmith.user.email.split('@')[0] || '%'
```

Slack `user_name` should align with the email local-part (or adjust the queries).

## After pull

1. Pull the branch in Appsmith  
2. Confirm datasource **Digests**  
3. Run page queries once; redeploy  
4. Sign in as an allowlisted admin and as a normal viewer to verify scoping  

## Schema reference

Digest.Bot migrations:

- `000_schema.sql` — `teammates`, `digests`
- `001_activity_timeline.sql` — `user_activities`, `digest_activities`
