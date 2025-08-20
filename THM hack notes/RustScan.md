is a port scanner like nmpa but fast but works best when combined 
this command does the trick :
sudo rustscan -a TARGET-IP \
  -r 1-65535 \
  --ulimit 5000 \
  --batch-size 500 \
  --timeout 3000 \
  -- -Pn -sS -sV -sC --reason --max-retries 2 --host-timeout 60s
#### 🧱 RustScan Layer:

| Flag               | Purpose                                         |
| ------------------ | ----------------------------------------------- |
| `-a 10.10.12.107`  | Target IP                                       |
| `-r 1-65535`       | Full TCP port range                             |
| `--ulimit 5000`    | Allow max open files (required for large scans) |
| `--batch-size 500` | Smaller batches to avoid dropped packets        |
| `--timeout 3000`   | 3-second timeout for slower responses           |
run 3 times to be sure ....!!!