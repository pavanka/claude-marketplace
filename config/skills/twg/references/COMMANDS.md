<!-- BEGIN COMMAND REFERENCE -->

# Reference Index

`COMMANDS.md` is the key-command index for this reference set.
It lists core commands and key flags, not every subcommand.
Use surface-local `--help` only when the needed subcommand is not listed or the surface is intentionally lazy.
Resolve `scripts/twg` relative to the skill directory, as described in `../SKILL.md`.

## Start here

- `ROUTING.md` — pick the right surface
- `GLOBAL-CONTRACT.md` — shared command patterns, filters, pagination, and write safety

## By surface family

- `FEDERATED-SURFACES.md` — `docs`, `videos`, `meetings`, `spaces`, `recently-viewed`, `pr`
- `PROJECTION-SURFACES.md` — `work query`, `org-tree`, `context jira workitem`
- `NATIVE-SURFACES.md` — `jira`, `confluence`, `bitbucket`/`bb`, `goals`, `projects`, `focus-areas`, `teams`, and related product surfaces
- `RELATION-AND-IDENTITY.md` — `user`, `user search`, `resolve`, `collaborators`
- `CONTROL-PLANE.md` — `login`, `skills`, `integrations`, `echo`

## Quick routing

- Cross-product request → `FEDERATED-SURFACES.md`
- Broad work/activity/hierarchy/context → `PROJECTION-SURFACES.md`
- Product-specific detail or mutation → `NATIVE-SURFACES.md`
- JSM ticket or service-project issue record → `twg jira workitem ...`
- JSM-native intake/config/service objects → `twg jsm ...`
- Need to resolve a person first → `RELATION-AND-IDENTITY.md`
- Need to tell the user about setup/package/runtime commands → `CONTROL-PLANE.md`

## Command catalog

**Note:** Most commands that target a specific Atlassian site accept `--site <hostname>`. Omitted below for brevity.

### Bitbucket (23 commands)

```
twg bb branch create
twg bb branch query --workspace <ws> --repo <repo>
twg bb commit query --workspace <ws> --repo <repo>
twg bb deployment query
twg bb pipeline get
twg bb pipeline query --workspace <ws> --repo <repo>
twg bb prs approve
twg bb prs comment create
twg bb prs create
twg bb prs decline
twg bb prs get --workspace <ws> --repo <repo>
twg bb prs merge
twg bb prs query --workspace <ws> --repo <repo> --scope <repo|me> --role <author|reviewer> --reviewer <nickname-or-account-id> --state <OPEN|MERGED|DECLINED>
twg bb prs remove-request-changes
twg bb prs request-changes
twg bb prs task create
twg bb prs unapprove
twg bb prs update
twg bb repo contributors
twg bb repo get
twg bb repo query --workspace <ws>
twg bb workspace get --workspace <ws>
twg bb workspace member query
```

### Confluence (44 commands)

```
twg confluence blog create --space-id <spaceId>
twg confluence blog delete
twg confluence blog get
twg confluence blog query --scope <scope> --space-id <spaceId>
twg confluence blog restrict
twg confluence blog restrictions
twg confluence blog unrestrict
twg confluence blog update
twg confluence database create --space-id <spaceId>
twg confluence database delete
twg confluence database get
twg confluence database query --space-id <spaceId>
twg confluence database restrict
twg confluence database restrictions
twg confluence database unrestrict
twg confluence folder create
twg confluence folder delete
twg confluence folder get
twg confluence folder restrict
twg confluence folder restrictions
twg confluence folder unrestrict
twg confluence page create --space <key> --title <title> --body <text>
twg confluence page get --body-format <adf|storage>
twg confluence page move --space-key <spaceKey>
twg confluence page restrict
twg confluence page restrictions
twg confluence page unrestrict
twg confluence page update --title <title> --body <text>
twg confluence search query --cql <query>
twg confluence space archive
twg confluence space create --key <key>
twg confluence space delete
twg confluence space get
twg confluence space query --keys <keys>
twg confluence space unarchive
twg confluence space update
twg confluence tree
twg confluence whiteboard create --space-id <spaceId>
twg confluence whiteboard delete
twg confluence whiteboard get
twg confluence whiteboard query --space-id <spaceId>
twg confluence whiteboard restrict
twg confluence whiteboard restrictions
twg confluence whiteboard unrestrict
```

Common whiteboard usage:
- `twg confluence whiteboard query --space-id <id-or-key>` to find whiteboards in a space or personal space.
- `twg confluence whiteboard get <id>` to inspect a whiteboard from its URL id; add include flags when you need collaborators, children, operations, or properties.
- `twg jira workitem link whiteboard` when the user wants to connect an existing whiteboard to a Jira issue.

### Focus Areas (3 commands)

```
twg focus-areas get <ari>
twg focus-areas query --scope <me|user>
twg focus-areas search
```

### Goals (9 commands)

```
twg goals --scope <all|me|user|org> --status <active|at_risk|off_track> --account-id <aaid>
twg goals archive
twg goals create
twg goals get <key>
twg goals link jira-align-project
twg goals query --scope <scope> --email <email>
twg goals status-update create
twg goals status-update update
twg goals update
```

### Jira (30 commands)

```
twg context jira workitem  # Explore and rank context around a Jira workitem (enriched…
twg jira board get
twg jira board query
twg jira space get
twg jira space issue-types --space <key>
twg jira space link page
twg jira space link space --space-id <spaceId>
twg jira space status query --space <key>
twg jira sprint complete
twg jira sprint create
twg jira sprint link retro-page
twg jira sprint start
twg jira sprint update
twg jira workitem comment create --body <text>
twg jira workitem comment delete
twg jira workitem comment update
twg jira workitem create --space <key> --type <type> --summary <title>
twg jira workitem delete
twg jira workitem get
twg jira workitem link-types query
twg jira workitem link goal
twg jira workitem link page
twg jira workitem link project
twg jira workitem link whiteboard
twg jira workitem priorities query
twg jira workitem query --jql <query>
twg jira workitem statuses query
twg jira workitem transition --transition <name>
twg jira workitem types query
twg jira workitem update
```

### Projects (8 commands)

```
twg projects --scope <me|org|user> --status <all|on_track|at_risk|off_track>
twg projects archive
twg projects create
twg projects get <key>
twg projects query --scope <scope> --since <since> --email <email>
twg projects status-update create
twg projects status-update update
twg projects update
```

### Teams (2 commands)

```
twg teams get <name-or-ari>
twg teams query --query <query>
```

### Cross-Product & Utilities (27 commands)

```
twg collaborators --account-id <aaid>  # Discover who collaborates most on the same work items.
twg commits
twg cypher --query <cypher-statement>  # Advanced: run raw Flock GraphStore cypherQueryV2 queries…
twg deployments
twg docs --since <since>  # Federated documents across Confluence + third-party…
twg docs get --since <since>
twg docs query --since <period> --account-id <aaid>
twg feedback
twg focus-areas-tree  # Show focus area hierarchy tree for a focus area
twg meetings --since <since>
twg meetings get --since <since>
twg meetings query --since <period> --account-id <aaid>
twg notifications
twg org-tree --account-id <aaid> --depth <n>  # Show organizational hierarchy tree for a user
twg pull-requests  # Show detailed information about a pull request including…
twg recently-viewed --since <period>
twg resolve --query <query>  # Resolve natural-language references into Atlassian ARIs
twg scopes describe
twg scopes products
twg scopes query --keyword <keyword>
twg scopes validate --scopes <scopes>
twg update
twg user --account-id <aaid>
twg user search --email <email> --name <name>
twg videos get
twg videos query --since <period> --account-id <aaid>
twg work query --scope <me|user|team> --account-id <aaid> --since <period>
```

### Other Products (top commands + --help for rest)

**Assets** (9 total, 7 more via `twg assets --help`)
```
twg assets object get
twg assets objects query
```

**Customer Service Management (CSM)** (6 total, 5 more via `twg csm --help`)
```
twg csm customer query
```

**Jira Align** (16 total, 15 more via `twg jira-align --help`)
```
twg jira-align projects query
```

**Jira Service Management (JSM)** (53 total, 50 more via `twg jsm --help`)
```
twg jsm incident get
twg jsm request-type query --query-term <queryTerm> --portal-id <portalId>
twg jsm service query --limit <number>
```

**Loom** (8 total, 8 more via `twg loom --help`)

**Rovo** (1 total, 0 more via `twg rovo --help`)
```
twg rovo search
```

**Talent** (4 total, 3 more via `twg talent --help`)
```
twg talent position query
```

**Trello** (27 total, 24 more via `twg trello --help`)
```
twg trello board get
twg trello card get
twg trello workspace get
```

*158 listed · 167 via --help.*

<!-- END COMMAND REFERENCE -->