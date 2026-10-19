# Git History থেকে `.env` File Remove করা

ভুল করে `.env` push হয়ে গেলে শুধু `.gitignore`-এ add করলে কাজ হয় না, কারণ file টা আগেই track হয়ে গেছে। পুরো commit history থেকে মুছতে নিচের steps follow করুন।

> ⚠️ কোনো secret (API key, password, token) leak হলে সবার আগে সেটা **rotate/revoke** করুন। History মুছলেও কেউ আগেই সেটা দেখে ফেলে থাকতে পারে।

---

## ১. `git-filter-repo` Install করুন

```bash
brew install git-filter-repo
```

> Homebrew না থাকলে: `pip3 install git-filter-repo` অথবা `python3 -m pip install git-filter-repo`

## ২. `.env` এর Backup নিন

History rewrite করার সময় local `.env` মুছে যেতে পারে, তাই আগে copy রাখুন।

```bash
cp .env /tmp/.env.backup
```

> শুরুর আগে `git status` দিয়ে check করুন। Uncommitted change থাকলে আগে commit বা stash করে নিন।

## ৩. History থেকে `.env` Remove করুন

```bash
git filter-repo --path .env --invert-paths --force
```

- `--path .env` → এই file টা target করবে
- `--invert-paths` → এই file বাদে বাকি সব রাখবে
- `--force` → fresh clone না হলেও চলবে

## ৪. Remote আবার Add করুন

`filter-repo` চালানোর পর `origin` remote মুছে যায়, তাই আবার add করতে হয়।

```bash
git remote add origin https://github.com/arifucoder/phovia-tour-client.git
```

## ৫. Force Push করুন

```bash
git push origin --force --all
git push origin --force --tags
```

## ৬. `.env` ফিরিয়ে আনুন

```bash
cp /tmp/.env.backup .env
```

---

## ✅ Verify করুন

```bash
git log --all -- .env
```

কিছু না দেখালে বুঝবেন history থেকে `.env` পুরোপুরি মুছে গেছে।

## 📝 মনে রাখবেন

- `.gitignore`-এ `.env` add করা আছে কিনা নিশ্চিত করুন, যাতে আবার push না হয়।
- `.env.example` রাখতে চাইলে `.gitignore`-এ `!.env.example` যোগ করুন।
- অন্য কেউ repo clone করে থাকলে তাদের নতুন করে clone করতে হবে, কারণ history বদলে গেছে।