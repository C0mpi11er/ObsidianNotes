# recon-ng — Quick Cheat Sheet

_A compact, practical reference for common recon-ng workflows, commands, and tips._

---

## 1. Getting started

- **Start recon-ng**
    

```bash
recon-ng
# or
python3 /path/to/recon-ng/recon-ng
```

- **Show help**
    

```text
help                     # global help
help <command>           # help for a specific command
```

- **Version**
    

```text
version
```

---

## 2. Workspaces (project isolation)

- **List workspaces**
    

```text
workspaces list
```

- **Create / switch**
    

```text
workspaces create myproject
workspaces select myproject
```

- **Delete workspace**
    

```text
workspaces delete myproject
```

- **Current workspace**
    

```text
workspaces show
```

---

## 3. The recon-ng layout

- `modules` — list and load modules
    
- `marketplace` — find and install modules
    
- `keys` — manage API keys
    
- `db` — interact with the database (sql)
    
- `hosts`, `contacts`, `emails`, `domains` — built-in data stores
    
- `report` — export results
    

---

## 4. Modules — basics

- **List available modules**
    

```text
show modules
```

- **Search modules**
    

```text
modules search <term>
```

- **Load a module**
    

```text
modules load recon/domains-hosts/google_site_web
# prompt changes to module context
```

- **Show a loaded module's options**
    

```text
show options
```

- **Set module options**
    

```text
set SOURCE example.com
set APIKEY <your-key>
```

- **Unset option**
    

```text
unset APIKEY
```

- **Run the module**
    

```text
run
# or
execute
```

- **Check required options**
    

`show info` — displays module description, author, required options

---

## 5. API Keys & credentials

- **List configured keys**
    

```text
keys list
```

- **Set a key**
    

```text
keys add <service> <key>
# example: keys add shodan APIKEY
```

- **Delete**
    

```text
keys delete <service>
```

- **Common services**: shodan, haveibeenpwned, virustotal, google, bing, hunter, censys, wigle, etc. (depends on installed modules)
    

---

## 6. Database & data stores

- **Show counts**
    

```text
show hosts
show domains
show contacts
show emails
```

- **Export database (CSV, JSON)**
    

```text
report csv /path/to/output.csv
report json /path/to/output.json
```

- **Direct SQL (advanced)**
    

```text
db query SELECT * FROM hosts LIMIT 50;
```

- **Import from file**
    

Many modules accept `SOURCE` values; there are also modules for importing lists (check marketplace modules like `import/csv`)

---

## 7. Marketplace (install extra modules)

- **Search marketplace**
    

```text
marketplace search <term>
```

- **Install**
    

```text
marketplace install <module_name>
```

- **Update marketplace**
    

```text
marketplace update
```


marketplace info <module_name>
# e.g.
marketplace info recon/hosts-hosts/shodan_hostname

---

## 8. Useful workflow snippets

### 8.1 Domain -> Hosts -> Passive DNS -> IPs -> ASN

1. `workspaces create recon-example && workspaces select recon-example`
    
2. `modules load recon/domains-hosts/bing_domain_web` (or any domain-to-hosts module)
    
3. `set SOURCE example.com` → `run`
    
4. `modules load recon/hosts-hosts/virustotal_ip` (or passive DNS module)
    
5. Set options & `run`
    
6. Use `show hosts` / `show domains` to inspect results
    

### 8.2 Email harvesting + breach checks

1. `modules load