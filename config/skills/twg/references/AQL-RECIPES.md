# AQL Recipes

Common AQL patterns for `twg assets query --aql "..." -s <site>`.

> AQL (Assets Query Language) queries objects by type, attribute, and relationships.
> Always pass `-s <site>` — Assets is site-scoped.

## By Object Type

```
objectType = Server
objectType = Laptop
objectType = "Virtual Machine"
objectType = "Virtual Machine" AND objectSchema = "IT Infrastructure"
```

## By Attribute Value

```
Name = 'Production DB'                                       # exact match
Name like 'Fender'                                           # contains
Name like 'prod-*'                                           # wildcard
Name startsWith 'web-'                                       # prefix
objectType = Server AND OS = Linux AND Status = Active       # combined filters
Status = Active                                              # by status
Label = Production                                           # by label
```

## By ID

```
objectId = 1187928
objectId IN (1187928, 1187929, 1187927)
```

## By Relationships

```
object HAVING inboundReferences(objectType = Application)
object HAVING outboundReferences(Name = "Core DB")
```

## Combined Examples

```bash
# All active Linux servers in production
scripts/twg assets query --aql "objectType = Server AND OS = Linux AND Status = Active"

# Laptops assigned to a user
scripts/twg assets query --aql "objectType = Laptop AND Assignee = 'jane@company.com'"

# Find Fender guitars
scripts/twg assets query --aql "objectType = Fender" --limit 10

# Wildcard name search
scripts/twg assets query --aql "Name like 'prod-*'"
```

## Pagination

Use `--limit <n>` to control result size. Default is 25 results per page.

```bash
scripts/twg assets query --aql "objectType = Server" --limit 50
```

## Tips

- Object type and attribute names are **case-sensitive**.
- String values can use single or double quotes: `Name = 'value'` or `Name = "value"`.
- Discover schemas: `twg assets objectschema get --id <id> -s <site>`
- Discover types: `twg assets type get --id <id> -s <site>`
- Discover attributes: `twg assets type list-attr query --type-id <id> -s <site>`
