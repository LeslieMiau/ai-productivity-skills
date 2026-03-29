# init.sh Templates by Stack

Use these as starting points. Customize based on what you detect in the actual project (existing scripts, config files, health endpoints).

## Python (FastAPI / Flask / Django)

```bash
#!/usr/bin/env bash
cd "$(dirname "$0")"
ERRORS=0

echo "=== 1. Environment ==="
if [ ! -d .venv ]; then
  python3 -m venv .venv
fi
if [ -f pyproject.toml ]; then
  .venv/bin/python -m pip install -q -e '.[dev]' 2>/dev/null || \
  .venv/bin/python -m pip install -q -e . 2>/dev/null || \
  { echo "⚠️  pip install failed"; ERRORS=$((ERRORS+1)); }
elif [ -f requirements.txt ]; then
  .venv/bin/python -m pip install -q -r requirements.txt || \
  { echo "⚠️  pip install failed"; ERRORS=$((ERRORS+1)); }
fi

echo "=== 2. Tests ==="
.venv/bin/pytest -x -q --tb=short 2>&1 | tail -15
[ "${PIPESTATUS[0]}" -ne 0 ] && ERRORS=$((ERRORS+1))

echo "=== 3. Dev Server ==="
# Adjust port and module to match the project
if curl -sS -o /dev/null http://127.0.0.1:8000/health 2>/dev/null; then
  echo "Server already running"
else
  .venv/bin/uvicorn main:app --host 127.0.0.1 --port 8000 --reload &
  sleep 3
fi

echo "=== 4. Health Check ==="
HEALTH=$(curl -sS -o /dev/null -w "%{http_code}" http://127.0.0.1:8000/health 2>/dev/null || echo "000")
[ "$HEALTH" = "200" ] && echo "✅ OK" || { echo "⚠️  HTTP $HEALTH"; ERRORS=$((ERRORS+1)); }

echo "=== Init complete (errors: $ERRORS) ==="
exit 0
```

## Node.js (Next.js / Express / Vite)

```bash
#!/usr/bin/env bash
cd "$(dirname "$0")"
ERRORS=0

echo "=== 1. Environment ==="
if [ ! -d node_modules ]; then
  if [ -f pnpm-lock.yaml ]; then
    pnpm install --frozen-lockfile 2>/dev/null || pnpm install
  elif [ -f yarn.lock ]; then
    yarn install --frozen-lockfile 2>/dev/null || yarn install
  else
    npm ci 2>/dev/null || npm install
  fi
fi

echo "=== 2. Tests ==="
if grep -q '"test"' package.json 2>/dev/null; then
  npm test 2>&1 | tail -15
  [ "${PIPESTATUS[0]}" -ne 0 ] && ERRORS=$((ERRORS+1))
fi

echo "=== 3. Dev Server ==="
# Adjust port to match the project
if curl -sS -o /dev/null http://127.0.0.1:3000 2>/dev/null; then
  echo "Server already running"
else
  npm run dev &
  sleep 5
fi

echo "=== 4. Health Check ==="
HEALTH=$(curl -sS -o /dev/null -w "%{http_code}" http://127.0.0.1:3000 2>/dev/null || echo "000")
[ "$HEALTH" = "200" ] && echo "✅ OK" || { echo "⚠️  HTTP $HEALTH"; ERRORS=$((ERRORS+1)); }

echo "=== Init complete (errors: $ERRORS) ==="
exit 0
```

## Go

```bash
#!/usr/bin/env bash
cd "$(dirname "$0")"
ERRORS=0

echo "=== 1. Environment ==="
go mod download || { echo "⚠️  go mod download failed"; ERRORS=$((ERRORS+1)); }

echo "=== 2. Tests ==="
go test ./... -count=1 -short 2>&1 | tail -15
[ "${PIPESTATUS[0]}" -ne 0 ] && ERRORS=$((ERRORS+1))

echo "=== 3. Dev Server ==="
# Adjust the main package path and port
if curl -sS -o /dev/null http://127.0.0.1:8080/health 2>/dev/null; then
  echo "Server already running"
else
  go run ./cmd/server &
  sleep 3
fi

echo "=== 4. Health Check ==="
HEALTH=$(curl -sS -o /dev/null -w "%{http_code}" http://127.0.0.1:8080/health 2>/dev/null || echo "000")
[ "$HEALTH" = "200" ] && echo "✅ OK" || { echo "⚠️  HTTP $HEALTH"; ERRORS=$((ERRORS+1)); }

echo "=== Init complete (errors: $ERRORS) ==="
exit 0
```

## Rust

```bash
#!/usr/bin/env bash
cd "$(dirname "$0")"
ERRORS=0

echo "=== 1. Environment ==="
cargo build 2>&1 | tail -5
[ "${PIPESTATUS[0]}" -ne 0 ] && ERRORS=$((ERRORS+1))

echo "=== 2. Tests ==="
cargo test 2>&1 | tail -15
[ "${PIPESTATUS[0]}" -ne 0 ] && ERRORS=$((ERRORS+1))

echo "=== Init complete (errors: $ERRORS) ==="
exit 0
```

## Static / No Server

For projects without a dev server (libraries, CLI tools, data pipelines):

```bash
#!/usr/bin/env bash
cd "$(dirname "$0")"
ERRORS=0

echo "=== 1. Environment ==="
# [stack-specific dependency install]

echo "=== 2. Tests ==="
# [stack-specific test runner]

echo "=== Init complete (errors: $ERRORS) ==="
exit 0
```

Skip the server and health check sections entirely. Don't generate dummy endpoints.

## Customization Notes

When generating init.sh, always check:
- Does the project have existing startup scripts? (`scripts/`, `Makefile`, `docker-compose.yml`) — reference them instead of reinventing
- What port does the dev server actually use? Read config files, don't assume defaults
- What's the actual health check endpoint? Search route files for `/health`, `/ping`, `/preflight`, `/api/health`
- Does the project need environment variables? Check for `.env.example` or `.env.template` and copy if needed
- Does the project use Docker? If so, the init might be `docker compose up -d` instead of native setup
