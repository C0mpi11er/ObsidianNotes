### ✅ Let's do a proper fix step-by-step:

---

## Thanks — the issue is persisting and still pointing to this fatal error:

> `Can't open and lock privilege tables`

This usually means MariaDB **cannot access system tables** inside the MySQL data directory (`/var/lib/mysql`). That points directly to either **permissions**, **missing files**, or **corrupt tables**.

---

### ✅ Let's do a proper fix step-by-step:

---

## 🔍 Step 1: Check the data directory

Check that `/var/lib/mysql` exists:

```bash
ls -l /var/lib | grep mysql
```

If it exists, list its contents:

```bash
ls -l /var/lib/mysql
```

Ensure it contains system tables like `mysql/`, `aria_log_control`, etc.

---

## 🔧 Step 2: Fix ownership

Ensure the correct ownership:

```bash
sudo chown -R mysql:mysql /var/lib/mysql
```

Then double-check with:

```bash
ls -ld /var/lib/mysql
```

It should show `mysql:mysql`.

---

## 🔧 Step 3: Run `mysql_install_db` (if missing system tables)

If the `mysql` system database is missing or corrupt, you’ll need to reinitialize it:

```bash
sudo mariadb-install-db --user=mysql --basedir=/usr --datadir=/var/lib/mysql
```

Then try again:

```bash
sudo systemctl restart mariadb
```

---

## 🧪 Step 4: Final test

Check the logs again:

```bash
sudo journalctl -xeu mariadb
```

If it starts successfully, then:

```bash
mysql -u root -p
```

---

Would you like me to generate a shell script to automate all these fixes on your Arch system?