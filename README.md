# K8s Assignment 5 — ConfigMaps & Secrets

> **Repo:** `galal21Mohamed/K8s_Assignment_5`
> **Topic:** Managing application configuration and sensitive data in Kubernetes using ConfigMaps and Secrets.

---

## Diagram 1 — Cluster Structure

> How ConfigMap & Secret live in the namespace and inject into the Pod

<p align="center">
<svg viewBox="0 0 780 400" width="100%" xmlns="http://www.w3.org/2000/svg" font-family="-apple-system,BlinkMacSystemFont,Segoe UI,sans-serif">
  <defs>
    <marker id="a1t" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
      <path d="M2 1L8 5L2 9" fill="none" stroke="#0F6E56" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
    </marker>
    <marker id="a1c" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
      <path d="M2 1L8 5L2 9" fill="none" stroke="#993C1D" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
    </marker>
  </defs>
  <rect x="10" y="10" width="760" height="375" rx="20" fill="#F1EFE8" stroke="#888780" stroke-width="1"/>
  <text x="32" y="38" font-size="13" font-weight="600" fill="#444441">Kubernetes cluster</text>
  <rect x="28" y="52" width="724" height="318" rx="14" fill="#E6F1FB" stroke="#185FA5" stroke-width="0.8"/>
  <text x="48" y="76" font-size="12" font-weight="600" fill="#0C447C">Namespace</text>
  <rect x="46" y="92" width="220" height="258" rx="10" fill="#E1F5EE" stroke="#0F6E56" stroke-width="0.8"/>
  <text x="156" y="116" text-anchor="middle" font-size="13" font-weight="600" fill="#085041">ConfigMap</text>
  <text x="156" y="132" text-anchor="middle" font-size="11" fill="#6b6b65">non-sensitive config</text>
  <rect x="62" y="144" width="188" height="28" rx="6" fill="#E1F5EE" stroke="#0F6E56" stroke-width="0.6"/>
  <text x="156" y="158" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#085041">APP_ENV = production</text>
  <rect x="62" y="180" width="188" height="28" rx="6" fill="#E1F5EE" stroke="#0F6E56" stroke-width="0.6"/>
  <text x="156" y="194" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#085041">APP_PORT = 8080</text>
  <rect x="62" y="216" width="188" height="28" rx="6" fill="#E1F5EE" stroke="#0F6E56" stroke-width="0.6"/>
  <text x="156" y="230" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#085041">APP_COLOR = blue</text>
  <rect x="62" y="252" width="188" height="28" rx="6" fill="#E1F5EE" stroke="#0F6E56" stroke-width="0.6"/>
  <text x="156" y="266" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#085041">MAX_CONNECTIONS = 100</text>
  <rect x="284" y="92" width="220" height="258" rx="10" fill="#FAECE7" stroke="#993C1D" stroke-width="0.8"/>
  <text x="394" y="116" text-anchor="middle" font-size="13" font-weight="600" fill="#712B13">Secret</text>
  <text x="394" y="132" text-anchor="middle" font-size="11" fill="#6b6b65">base64-encoded</text>
  <rect x="300" y="144" width="188" height="28" rx="6" fill="#FAECE7" stroke="#993C1D" stroke-width="0.6"/>
  <text x="394" y="158" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#712B13">DB_USER = ████</text>
  <rect x="300" y="180" width="188" height="28" rx="6" fill="#FAECE7" stroke="#993C1D" stroke-width="0.6"/>
  <text x="394" y="194" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#712B13">DB_PASSWORD = ████</text>
  <rect x="300" y="216" width="188" height="28" rx="6" fill="#FAECE7" stroke="#993C1D" stroke-width="0.6"/>
  <text x="394" y="230" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#712B13">DB_NAME = ████</text>
  <rect x="522" y="92" width="210" height="258" rx="10" fill="#EEEDFE" stroke="#534AB7" stroke-width="0.8"/>
  <text x="627" y="116" text-anchor="middle" font-size="13" font-weight="600" fill="#3C3489">Pod</text>
  <text x="627" y="132" text-anchor="middle" font-size="11" fill="#6b6b65">running container</text>
  <rect x="538" y="148" width="178" height="56" rx="6" fill="#EEEDFE" stroke="#534AB7" stroke-width="0.6"/>
  <text x="627" y="170" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#3C3489">env vars injected</text>
  <text x="627" y="188" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#3C3489">decoded · plain text</text>
  <rect x="538" y="216" width="178" height="56" rx="6" fill="#EEEDFE" stroke="#534AB7" stroke-width="0.6"/>
  <text x="627" y="238" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#3C3489">app reads config</text>
  <text x="627" y="256" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#3C3489">at startup</text>
  <line x1="266" y1="221" x2="282" y2="221" stroke="#0F6E56" stroke-width="1" marker-end="url(#a1t)" fill="none"/>
  <text x="274" y="214" text-anchor="middle" font-size="10" fill="#6b6b65">envFrom</text>
  <line x1="504" y1="221" x2="520" y2="221" stroke="#993C1D" stroke-width="1" marker-end="url(#a1c)" fill="none"/>
  <text x="512" y="214" text-anchor="middle" font-size="10" fill="#6b6b65">inject</text>
</svg>
</p>

---

## Diagram 2 — Injection Methods: `envFrom` vs `env`

> `envFrom` pulls all keys at once — `env` lets you pick specific ones (why `MAX_CONNECTIONS` was invisible in Q3)

<p align="center">
<svg viewBox="0 0 780 320" width="100%" xmlns="http://www.w3.org/2000/svg" font-family="-apple-system,BlinkMacSystemFont,Segoe UI,sans-serif">
  <defs>
    <marker id="a2t" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
      <path d="M2 1L8 5L2 9" fill="none" stroke="#0F6E56" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
    </marker>
    <marker id="a2b" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
      <path d="M2 1L8 5L2 9" fill="none" stroke="#534AB7" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
    </marker>
    <marker id="a2bl" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
      <path d="M2 1L8 5L2 9" fill="none" stroke="#185FA5" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
    </marker>
  </defs>
  <rect x="10" y="30" width="170" height="260" rx="10" fill="#E1F5EE" stroke="#0F6E56" stroke-width="0.8"/>
  <text x="95" y="54" text-anchor="middle" font-size="13" font-weight="600" fill="#085041">ConfigMap</text>
  <rect x="24" y="66" width="142" height="26" rx="5" fill="#E1F5EE" stroke="#0F6E56" stroke-width="0.5"/>
  <text x="95" y="79" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#085041">APP_ENV</text>
  <rect x="24" y="100" width="142" height="26" rx="5" fill="#E1F5EE" stroke="#0F6E56" stroke-width="0.5"/>
  <text x="95" y="113" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#085041">APP_PORT</text>
  <rect x="24" y="134" width="142" height="26" rx="5" fill="#E1F5EE" stroke="#0F6E56" stroke-width="0.5"/>
  <text x="95" y="147" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#085041">APP_COLOR</text>
  <rect x="24" y="168" width="142" height="26" rx="5" fill="#E1F5EE" stroke="#0F6E56" stroke-width="0.5"/>
  <text x="95" y="181" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#085041">MAX_CONN</text>
  <rect x="220" y="30" width="160" height="118" rx="10" fill="#E6F1FB" stroke="#185FA5" stroke-width="0.8"/>
  <text x="300" y="53" text-anchor="middle" font-size="13" font-weight="600" fill="#0C447C">envFrom</text>
  <text x="300" y="69" text-anchor="middle" font-size="11" fill="#6b6b65">imports ALL keys</text>
  <rect x="234" y="80" width="132" height="54" rx="6" fill="#E6F1FB" stroke="#185FA5" stroke-width="0.5"/>
  <text x="300" y="100" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#0C447C">Pod gets all 4 vars</text>
  <text x="300" y="118" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#0C447C">one line of YAML</text>
  <rect x="220" y="172" width="160" height="118" rx="10" fill="#EEEDFE" stroke="#534AB7" stroke-width="0.8"/>
  <text x="300" y="195" text-anchor="middle" font-size="13" font-weight="600" fill="#3C3489">env (selective)</text>
  <text x="300" y="211" text-anchor="middle" font-size="11" fill="#6b6b65">pick specific keys</text>
  <rect x="234" y="222" width="132" height="54" rx="6" fill="#EEEDFE" stroke="#534AB7" stroke-width="0.5"/>
  <text x="300" y="242" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#3C3489">Pod gets only</text>
  <text x="300" y="260" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#3C3489">COLOR + PORT</text>
  <line x1="166" y1="79" x2="218" y2="68" stroke="#0F6E56" stroke-width="1" marker-end="url(#a2t)" fill="none"/>
  <line x1="166" y1="113" x2="218" y2="80" stroke="#0F6E56" stroke-width="1" marker-end="url(#a2t)" fill="none"/>
  <line x1="166" y1="147" x2="218" y2="92" stroke="#0F6E56" stroke-width="1" marker-end="url(#a2t)" fill="none"/>
  <line x1="166" y1="181" x2="218" y2="104" stroke="#0F6E56" stroke-width="1" marker-end="url(#a2t)" fill="none"/>
  <line x1="166" y1="113" x2="218" y2="228" stroke="#534AB7" stroke-width="1" stroke-dasharray="5 3" marker-end="url(#a2b)" fill="none"/>
  <line x1="166" y1="147" x2="218" y2="245" stroke="#534AB7" stroke-width="1" stroke-dasharray="5 3" marker-end="url(#a2b)" fill="none"/>
  <rect x="420" y="30" width="350" height="260" rx="10" fill="#F1EFE8" stroke="#888780" stroke-width="0.8"/>
  <text x="595" y="54" text-anchor="middle" font-size="13" font-weight="600" fill="#444441">Pod environment</text>
  <rect x="436" y="68" width="318" height="98" rx="8" fill="#E6F1FB" stroke="#185FA5" stroke-width="0.6"/>
  <text x="595" y="88" text-anchor="middle" font-size="11" fill="#0C447C">via envFrom — all 4 keys</text>
  <rect x="448" y="98" width="134" height="24" rx="4" fill="#E6F1FB" stroke="#185FA5" stroke-width="0.5"/>
  <text x="515" y="110" text-anchor="middle" dominant-baseline="central" font-size="10" fill="#0C447C">APP_ENV</text>
  <rect x="590" y="98" width="134" height="24" rx="4" fill="#E6F1FB" stroke="#185FA5" stroke-width="0.5"/>
  <text x="657" y="110" text-anchor="middle" dominant-baseline="central" font-size="10" fill="#0C447C">APP_PORT</text>
  <rect x="448" y="128" width="134" height="24" rx="4" fill="#E6F1FB" stroke="#185FA5" stroke-width="0.5"/>
  <text x="515" y="140" text-anchor="middle" dominant-baseline="central" font-size="10" fill="#0C447C">APP_COLOR</text>
  <rect x="590" y="128" width="134" height="24" rx="4" fill="#E6F1FB" stroke="#185FA5" stroke-width="0.5"/>
  <text x="657" y="140" text-anchor="middle" dominant-baseline="central" font-size="10" fill="#0C447C">MAX_CONN</text>
  <rect x="436" y="180" width="318" height="96" rx="8" fill="#EEEDFE" stroke="#534AB7" stroke-width="0.6"/>
  <text x="595" y="200" text-anchor="middle" font-size="11" fill="#3C3489">via env — 2 keys only</text>
  <rect x="476" y="212" width="134" height="24" rx="4" fill="#EEEDFE" stroke="#534AB7" stroke-width="0.5"/>
  <text x="543" y="224" text-anchor="middle" dominant-baseline="central" font-size="10" fill="#3C3489">APP_COLOR</text>
  <rect x="618" y="212" width="118" height="24" rx="4" fill="#EEEDFE" stroke="#534AB7" stroke-width="0.5"/>
  <text x="677" y="224" text-anchor="middle" dominant-baseline="central" font-size="10" fill="#3C3489">APP_PORT</text>
  <text x="595" y="261" text-anchor="middle" font-size="11" fill="#6b6b65">MAX_CONN not visible ✕</text>
  <line x1="382" y1="89" x2="418" y2="112" stroke="#185FA5" stroke-width="1" marker-end="url(#a2bl)" fill="none"/>
  <line x1="382" y1="231" x2="418" y2="216" stroke="#534AB7" stroke-width="1" marker-end="url(#a2b)" fill="none"/>
</svg>
</p>

---

## Diagram 3 — Decision Flowchart

> One question decides everything: is the value sensitive?

<p align="center">
<svg viewBox="0 0 780 500" width="100%" xmlns="http://www.w3.org/2000/svg" font-family="-apple-system,BlinkMacSystemFont,Segoe UI,sans-serif">
  <defs>
    <marker id="a3g" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
      <path d="M2 1L8 5L2 9" fill="none" stroke="#888780" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
    </marker>
    <marker id="a3t" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
      <path d="M2 1L8 5L2 9" fill="none" stroke="#0F6E56" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
    </marker>
    <marker id="a3c" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
      <path d="M2 1L8 5L2 9" fill="none" stroke="#993C1D" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
    </marker>
  </defs>
  <rect x="290" y="14" width="200" height="44" rx="22" fill="#F1EFE8" stroke="#888780" stroke-width="0.8"/>
  <text x="390" y="36" text-anchor="middle" dominant-baseline="central" font-size="13" font-weight="600" fill="#444441">New config value</text>
  <line x1="390" y1="58" x2="390" y2="96" stroke="#888780" stroke-width="1" marker-end="url(#a3g)" fill="none"/>
  <polygon points="390,96 510,140 390,184 270,140" fill="#EEEDFE" stroke="#534AB7" stroke-width="0.8"/>
  <text x="390" y="136" text-anchor="middle" dominant-baseline="central" font-size="13" font-weight="600" fill="#3C3489">Is it sensitive?</text>
  <text x="390" y="156" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#6b6b65">password · token · key</text>
  <line x1="270" y1="140" x2="142" y2="140" stroke="#888780" stroke-width="1" marker-end="url(#a3g)" fill="none"/>
  <text x="206" y="132" text-anchor="middle" font-size="11" fill="#6b6b65">No</text>
  <line x1="510" y1="140" x2="638" y2="140" stroke="#888780" stroke-width="1" marker-end="url(#a3g)" fill="none"/>
  <text x="574" y="132" text-anchor="middle" font-size="11" fill="#6b6b65">Yes</text>
  <rect x="32" y="196" width="220" height="56" rx="10" fill="#E1F5EE" stroke="#0F6E56" stroke-width="0.8"/>
  <text x="142" y="220" text-anchor="middle" dominant-baseline="central" font-size="14" font-weight="600" fill="#085041">ConfigMap</text>
  <text x="142" y="238" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#6b6b65">plain text · not encrypted</text>
  <line x1="142" y1="140" x2="142" y2="194" stroke="#0F6E56" stroke-width="1" marker-end="url(#a3t)" fill="none"/>
  <rect x="528" y="196" width="220" height="56" rx="10" fill="#FAECE7" stroke="#993C1D" stroke-width="0.8"/>
  <text x="638" y="220" text-anchor="middle" dominant-baseline="central" font-size="14" font-weight="600" fill="#712B13">Secret</text>
  <text x="638" y="238" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#6b6b65">base64-encoded</text>
  <line x1="638" y1="140" x2="638" y2="194" stroke="#993C1D" stroke-width="1" marker-end="url(#a3c)" fill="none"/>
  <rect x="22" y="276" width="240" height="200" rx="10" fill="#E1F5EE" stroke="#0F6E56" stroke-width="0.6"/>
  <text x="142" y="298" text-anchor="middle" font-size="12" font-weight="600" fill="#085041">Examples</text>
  <rect x="36" y="310" width="212" height="26" rx="5" fill="#E1F5EE" stroke="#0F6E56" stroke-width="0.5"/>
  <text x="142" y="323" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#085041">APP_ENV = production</text>
  <rect x="36" y="342" width="212" height="26" rx="5" fill="#E1F5EE" stroke="#0F6E56" stroke-width="0.5"/>
  <text x="142" y="355" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#085041">APP_PORT = 8080</text>
  <rect x="36" y="374" width="212" height="26" rx="5" fill="#E1F5EE" stroke="#0F6E56" stroke-width="0.5"/>
  <text x="142" y="387" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#085041">APP_COLOR = blue</text>
  <rect x="36" y="406" width="212" height="26" rx="5" fill="#E1F5EE" stroke="#0F6E56" stroke-width="0.5"/>
  <text x="142" y="419" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#085041">MAX_CONNECTIONS = 100</text>
  <rect x="36" y="438" width="212" height="26" rx="5" fill="#E1F5EE" stroke="#0F6E56" stroke-width="0.5"/>
  <text x="142" y="451" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#085041">feature flags · UI color</text>
  <line x1="142" y1="252" x2="142" y2="274" stroke="#0F6E56" stroke-width="1" marker-end="url(#a3t)" fill="none"/>
  <rect x="518" y="276" width="240" height="200" rx="10" fill="#FAECE7" stroke="#993C1D" stroke-width="0.6"/>
  <text x="638" y="298" text-anchor="middle" font-size="12" font-weight="600" fill="#712B13">Examples</text>
  <rect x="532" y="310" width="212" height="26" rx="5" fill="#FAECE7" stroke="#993C1D" stroke-width="0.5"/>
  <text x="638" y="323" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#712B13">DB_USER = admin</text>
  <rect x="532" y="342" width="212" height="26" rx="5" fill="#FAECE7" stroke="#993C1D" stroke-width="0.5"/>
  <text x="638" y="355" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#712B13">DB_PASSWORD = p@ssw0rd</text>
  <rect x="532" y="374" width="212" height="26" rx="5" fill="#FAECE7" stroke="#993C1D" stroke-width="0.5"/>
  <text x="638" y="387" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#712B13">DB_NAME = myapp</text>
  <rect x="532" y="406" width="212" height="26" rx="5" fill="#FAECE7" stroke="#993C1D" stroke-width="0.5"/>
  <text x="638" y="419" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#712B13">API_KEY = ████████</text>
  <rect x="532" y="438" width="212" height="26" rx="5" fill="#FAECE7" stroke="#993C1D" stroke-width="0.5"/>
  <text x="638" y="451" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#712B13">TLS certs · OAuth tokens</text>
  <line x1="638" y1="252" x2="638" y2="274" stroke="#993C1D" stroke-width="1" marker-end="url(#a3c)" fill="none"/>
  <rect x="280" y="296" width="220" height="156" rx="10" fill="#F1EFE8" stroke="#888780" stroke-width="0.6"/>
  <text x="390" y="318" text-anchor="middle" font-size="12" font-weight="600" fill="#444441">Both inject into Pod as</text>
  <text x="390" y="334" text-anchor="middle" font-size="11" fill="#6b6b65">plain-text env vars</text>
  <rect x="294" y="348" width="192" height="38" rx="6" fill="#F1EFE8" stroke="#888780" stroke-width="0.5"/>
  <text x="390" y="362" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#444441">envFrom → all keys</text>
  <text x="390" y="378" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#444441">env → selective keys</text>
  <rect x="294" y="398" width="192" height="42" rx="6" fill="#F1EFE8" stroke="#888780" stroke-width="0.5"/>
  <text x="390" y="414" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#444441">Secrets hidden in describe</text>
  <text x="390" y="430" text-anchor="middle" dominant-baseline="central" font-size="11" fill="#444441">ConfigMap visible</text>
  <line x1="264" y1="374" x2="278" y2="374" stroke="#888780" stroke-width="1" stroke-dasharray="4 3" marker-end="url(#a3g)" fill="none"/>
  <line x1="516" y1="374" x2="502" y2="374" stroke="#888780" stroke-width="1" stroke-dasharray="4 3" marker-end="url(#a3g)" fill="none"/>
</svg>
</p>

---

## Q&A

### Q1 — What is the difference between a ConfigMap and a hardcoded variable?

| | Hardcoded | ConfigMap |
|---|---|---|
| Location | Inside the Pod manifest | External Kubernetes object |
| Updates | Requires redeployment | Updated independently |
| Reuse | Not reusable | Shared across multiple Pods |

**Answer:** Hardcoded variables are written directly into the Pod manifest, so any change requires redeploying the Pod. A ConfigMap stores configuration outside the Pod and can be easily updated or reused across multiple workloads without touching the manifest.

---

### Q2 — What did you see from `grep APP`? Difference between `envFrom` and `env` keys?

**grep APP result:** The environment variables created imperatively were injected into the Pod and visible in the output.

| Method | Behavior | Use when |
|---|---|---|
| `envFrom` | Imports **all** key-value pairs from a ConfigMap at once | You need the full config |
| `env` | Maps variables **one by one** individually | You need specific keys only |

---

### Q3 — Is `MAX_CONNECTIONS` visible? Why not?

**No.** Selective injection was used — only the `COLOR` and `PORT` keys were explicitly referenced from the ConfigMap. `MAX_CONNECTIONS` was never mapped.

**When to use selective injection instead of `envFrom`:** When you only need one or a few specific key-value pairs, not the entire ConfigMap. This keeps each Pod's environment minimal and avoids exposing unnecessary configuration.

---

### Q4 — How are values stored in a Secret YAML? Why is Base64 NOT encryption?

**Storage format:** Values are stored as Base64-encoded strings.

```yaml
apiVersion: v1
kind: Secret
data:
  DB_USER: YWRtaW4=              # base64("admin")
  DB_PASSWORD: cEBzc3cwcmQxMjM=  # base64("p@ssw0rd123")
  DB_NAME: bXlhcHA=              # base64("myapp")
```

**Why Base64 is not encryption:**
Base64 is an **encoding** scheme — it changes how data is *represented*, not how it is *protected*. Anyone can decode it trivially with `base64 --decode`. Real protection comes from Kubernetes RBAC, encryption at rest, and external secret managers (e.g. HashiCorp Vault, AWS Secrets Manager).

---

### Q5 — Are values decoded in logs? Visible in `kubectl describe pod`?

| Location | Visible? | Details |
|---|---|---|
| Container logs | ✅ Yes | Kubernetes decodes automatically at injection |
| `kubectl describe pod` | ❌ No | Secret values are redacted to prevent accidental leaks |

---

### Q6 — What variables were configured? How to decide what goes where?

#### ConfigMap — non-sensitive config

```
APP_ENV=production
APP_PORT=8080
APP_COLOR=blue
MAX_CONNECTIONS=100
```

#### Secret — sensitive data

```
DB_USER=admin
DB_PASSWORD=p@ssw0rd123
DB_NAME=myapp
```

| Goes in ConfigMap | Goes in Secret |
|---|---|
| App mode / environment | Passwords |
| Port numbers | API keys & tokens |
| Feature flags | Database credentials |
| Connection limits | TLS certificates |
| UI color / theming | OAuth secrets |

---

### Q7 — Did both Pods have the same vars? Advantage over hardcoding?

**Yes.** All Pods created by a Deployment share identical environment variables because they all use the same Pod template, which references the same ConfigMap and Secret.

| Benefit | Details |
|---|---|
| **Separation of concerns** | Config lives outside the application manifest |
| **Easy updates** | Change config without redeploying the app |
| **Security** | Sensitive data stays out of plain YAML definitions |
| **Consistency** | All replicas receive identical values automatically |

---

## Summary

| Feature | ConfigMap | Secret |
|---|---|---|
| Use case | Non-sensitive config | Sensitive data |
| Storage format | Plain text | Base64-encoded |
| Inject all keys | `envFrom` | `envFrom` |
| Inject specific keys | `env` (selective) | `env` (selective) |
| Visible in `kubectl describe` | ✅ Yes | ❌ No |
| Visible in container logs | ✅ Yes | ✅ Yes (decoded) |

---

*Assignment 5 — Kubernetes Configuration Management*
