# NetOrca Backstage Integration

This repository contains NetOrca plugins and extensions required for [NetOrca](https://netorca.io) Backstage integration.

> **Note:** This project uses LDAP groups to connect your NetOrca and Backstage users/groups via [`@backstage/plugin-catalog-backend-module-ldap`](https://www.npmjs.com/package/@backstage/plugin-catalog-backend-module-ldap).  
> **Note2:** For scaffolder please make sure to integrate Gitlab/Github into your backstage instance in your config.yaml. Check [`official guide`](https://backstage.io/docs/integrations/gitlab/locations/)

---

## Plugins Overview
For the full NetOrca Backstage experience, make sure to install and configure all the plugins listed below and follow Onboarding checklist.


### 🔌 `@netautomate/plugin-netorca`

The main **frontend plugin** for NetOrca, exposing core functionalities such as the dashboard and team config management.

#### Installation

```bash
yarn workspace app add @netautomate/plugin-netorca
```

#### Configuration

1. **Update your `packages/app/src/App.tsx`:**
   Add the custom widget for Scaffolder templates and mount the NetOrca dashboard.

```tsx
import { DashboardPage } from '@netautomate/plugin-netorca';
import { ScaffolderFieldExtensions } from '@backstage/plugin-scaffolder-react';
import { NetorcaServiceItemsWidgetFieldExtension } from '@netautomate/plugin-netorca';

...

<Route path="/create" element={<ScaffolderPage />}>
  <ScaffolderFieldExtensions>
    <NetorcaServiceItemsWidgetFieldExtension />
  </ScaffolderFieldExtensions>
</Route>

<FlatRoutes>
  ...
  <Route path="/netorca" element={<DashboardPage />} />
</FlatRoutes>
```

2. **Add to Sidebar**

In `packages/app/src/components/Root/Root.tsx`:

```tsx
import AutoModeIcon from '@material-ui/icons/Autorenew';

...

<SidebarItem icon={AutoModeIcon} to="netorca" text="NetOrca" />
```

---

### 🔌 `@netautomate/plugin-netorca-backend`

The **backend plugin** used by the Scaffolder and frontend plugin. It connects to the NetOrca API and manages configuration.

#### Installation

```bash
yarn workspace backend add @netautomate/plugin-netorca-backend
```

#### Configuration

1. **Register in `packages/backend/src/index.ts`:**

```ts
backend.add(import('@netautomate/plugin-netorca-backend'));
```

2. **Add config values:**

```yaml
vault:
  baseUrl: http://127.0.0.1:8200
  token: hashicorp-vault-token
  secretPath: netorca

netorca:
  apiUrl: https://api.demo.netorca.io/v1
  apiKey: nXzecHzX.xxx
  defaultTeam: default
  rootURL: https://demo.netorca.io
  syncFrequency: 120 # in seconds – used by custom NetOrca entity provider to export all entities every 60 seconds using the API key
  syncTimeout: 20    # in seconds – timeout for each sync in Netorca entity provider
```

---

### 🔌 `@netautomate/plugin-catalog-backend-module-netorca-provider` and `@netautomate/plugin-scaffolder-backend-module-netorca-get-serviceitems`

The **NetOrca Provider backend module**. It exports NetOrca Services, Service Items, and Applications into Backstage and allows Create/Modify/Delete via Scaffolder Template.

#### Installation

```bash
yarn workspace backend add @netautomate/plugin-catalog-backend-module-netorca-provider
yarn workspace backend add @netautomate/plugin-scaffolder-backend-module-netorca-get-serviceitems

```
backend.add(import('@netautomate/plugin-catalog-backend-module-netorca-provider'));
backend.add(import('@netautomate/plugin-scaffolder-backend-module-netorca-get-serviceitems'));
#### Configuration

1. **Register in `packages/backend/src/index.ts`:**

```ts
backend.add(import('@netautomate/plugin-catalog-backend-module-netorca-provider'));
```

---


## ✅ Onboarding Checklist

1. Prepare your `.netorca/config.yaml` config in your `Consumer Repository` - include `backstage_repository` in team metadata. Example .config.yaml
```yaml 
netorca_global:
  base_url: https://netorca.example.com/v1
  metadata:
    team_name: charlie
    backstage_repository: gitlab.com/example/demos/networkorg/consumers/charlie
```
2. Prepare your [Hashicorp Vault](https://hub.docker.com/r/hashicorp/vault) instance - that'll be needed to store Consumer API Keys 
3. Review the `resources/docker-example/Dockerfile` setup.
4. `netorca-demo` folder contains example app using netorca-plugins in case of problems.
   > If you're using a custom Dockerfile, ensure it includes a `templ` folder with a `svc` template used by the Scaffolder to create new SVC files.
