# EOStorage.js (The Monolith Edition)

(The Update)
Philosophy: One File. One Truth.

![Version](https://img.shields.io/badge/Version-5.2.3-blue) 
<details>
<summary>Privacy & Security</summary>

I don't have a server. I don't want your data. If you use the Encryption plugin, even you might not be able to read your data if you lose the key. Everything lives 100% locally on your machine via IndexedDB.
</details>

## What is this?

EOStorage is a wrapper for IndexedDB. But it is not a normal wrapper. It is a complete, dependency-free, offline-first storage engine built because I got tired of race conditions destroying my data.

I built this to handle the heavy lifting for my projects (saving RPG game states and encrypted user configs). It was mainly for me and people like me who hate complexity. It is not a product, so don't treat it like a product.

## 🚀 The Architecture (How it Actually Works)

Unlike standard storage libraries that barely wrap the native API, EOStorage is built different.

**1. The "Time Machine"** (Conflict Resolution)

Concept: If two tabs try to update the same record (like a counter or a game save) at the exact same millisecond.
Reality: In standard libs, the last one wins and data is lost.
The Fix: EOStorage detects the version conflict, waits a random amount of "jitter" time, and retries. It works so well it feels like cheating.

**2. The Middleware Engine**

Concept: I didn't want to write window.crypto boilerplate in every single project.
Solution: You chain plugins. Encryption and Compression happen automatically before the data even touches the disk.
Justification: This separates your business logic from your storage logic. You just call .set(), and the Monolith handles the GZIP and AES-GCM silently.

**3. The "Monolith" Structure**

Reality: It is one file.
Justification: I don't want to maintain a package.json with 50 vulnerabilities just to save a JSON object. You copy this file, you paste it, and you go home.

## 🛠️ Usage

**Initialization (The "Kitchen Sink" Setup)**

```javascript
import { EOStorage } from './eostorage.js';

const db = new EOStorage({ dbName: 'MyProject_DB' });

// Chain them. Compression first (saves space), Encryption second (saves sanity).
db.use(EOStorage.Plugins.Compression(1024)) 
  .use(EOStorage.Plugins.Encryption('CORRECT_HORSE_BATTERY_STAPLE')); 

await db.init();

```

**The "Time Machine" in Action**

Use this when you know you're going to break things with concurrency.

```javascript
await db.update('global_counter', (val) => {
    // If another tab touches this value while we are thinking, 
    // the Time Machine will handle the paradox.
    return (val || 0) + 1;
});

```

**Streaming Export**

For when your JSON is too big for the V8 engine to handle without crying.

```javascript
for await (const chunk of db.exportJSON()) {
    // Stream it to a file, or just log it and feel powerful.
    console.log(chunk);
}

```

## 💾 Installation

**1. Copy eostorage.js.**

**2. Paste eostorage.js.**

**3. Done.**

(If you were looking for an npm command, you are in the wrong place. We don't do that here.)

## 🧭 Privacy & Security

The Promise: I have no servers. I have no analytics. The code runs on your metal.
The Warning: The encryption is only as good as your secret key. If you hardcode your password in the frontend, that's on you, not me.



> Built by Pyouneetm
If the code looks weird, it's probably an "optimization." It works perfectly for me, so it works.
