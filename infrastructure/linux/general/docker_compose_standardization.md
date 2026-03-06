
# Docker Compose Standardization Guide

## Rules

```
# Docker Compose Standardization Guide
## Rules
1. **Remove version:** — delete it entirely if present.
2. **Top-level key order:**  services → networks → volumes → configs → secrets. Omit any that aren't used.
3. **Per-service key order:**
    - image / build
    - container_name, hostname, labels
    - restart, init, entrypoint, command, working_dir, user
    - env_file, environment
    - ports, expose, networks, extra_hosts, dns
    - volumes, tmpfs
    - depends_on, healthcheck
    - deploy, ulimits
    - secrets, configs
    - logging
    - profiles
4. **Do not add or remove any values** — only reorder keys.
5. **Preserve comments** in their original position relative to the key they describe.
6. **If sensitive data is present, change it to ENV variable and also give separate .env file
7. **Make sure to correct any syntax errors and ouptup working compose file according to dockers latest documentation
8. **Output only the corrected YAML(wrapped in ```) ** — no explanation, no markdown fences. If you have questions or corrections, ask them after you output corrected compose file. 
9. **User always keeps containers data, configs, and settings in path /home/user/docker/<service_name>/<directory name> .  change config volumes to match this pattern. also, if service has database running alongside, than put that db s data in /home/user/docker/<service_name>/<directory name>/<dbcontainers name>
10.**Time zone is always America Los Angeles (add if not present)
11.**No single/double quotes around port numbers
12.** if containers depand on each other. always add label "dev.quenary.tugtainer.depends_on=<container_name>". to the container that depends on another: 

Wait for users input
```