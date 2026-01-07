
---

# מעבדה: זרימת עבודה בצוות עם Git וריפוזיטוריות מרוחקות

**משך זמן:** 90–120 דקות
**רמת קושי:** בינוני
**דרישות מוקדמות:** היכרות בסיסית עם טרמינל ועורך טקסט

---

## סקירה כללית

מעבדה זו מלמדת אותך לעבוד כמו צוות פיתוח מקצועי תוך שימוש ב-Git עם ריפוזיטורי מרוחק (GitHub, GitLab או Bitbucket). תלמד כיצד לאתחל ריפוזיטורי מקומי, לחבר אותו לשרת מרוחק, לוודא שהכל עובד בכל שלב, ולבצע זרימת עבודה מקצועית של סניפים (branching/merging) כפי שמקובל בסביבות פרודקשן.

**תוצאות למידה עיקריות:**

* להבין את הקשר בין ריפוזיטורי מקומי לריפוזיטורי מרוחק
* לשלוט במעקב אחר upstream ובניהול סניפים
* לבצע אסטרטגיית סניפים מקצועית (main/dev/feature/*)
* לפתור בעיות נפוצות בעת push/pull
* לוודא תקינות המערכת בכל שלב

---

## חלק 0: מודל מנטלי ובדיקת סביבה מוקדמת

### הבנת ארכיטקטורת Git

לפני שנכנסים לפקודות, חשוב להבין את שלושת השכבות במערכת:

#### **1. ריפוזיטורי מקומי (.git directory)**

* נמצא במחשב שלך בתיקיית `.git`
* מכיל את כל הקומיטים, הסניפים, אזור ה-staging והיסטוריה
* עצמאי לחלוטין – עובד גם בלי חיבור אינטרנט
* "מקור האמת" שלך עד שאתה מבצע push

#### **2. ריפוזיטורי מרוחק (שרת)**

* נמצא על שרתי GitHub/GitLab/Bitbucket
* משמש כמקור אמת משותף לצוות
* מקבל push ומשרת pull
* **לא** מתעדכן אוטומטית עם הריפוזיטורי המקומי

#### **3. שם המרוחק "origin"**

* כינוי לכתובת URL של הריפוזיטורי המרוחק
* קונבנציה: המרוחק הראשי נקרא תמיד "origin"
* אפשר להגדיר כמה מרוחקים (למשל "upstream", "fork")
* רק מצביע – שינוי השם לא משנה את השרת עצמו

#### **4. Upstream Tracking**

* קישור בין סניף מקומי לסניף מרוחק
* דוגמה: `main` מקומי עוקב אחרי `origin/main`
* מאפשר פקודות פשוטות `git push` ו-`git pull` בלי לציין יעד
* **קריטי:** בלי מעקב, Git לא יודע לאן לדחוף (push)

**סיכום המודל המנטלי:**

| המחשב שלך                             | שרת מרוחק                 |
| ------------------------------------- | ------------------------- |
| ריפוזיטורי מקומי (.git)               | ריפוזיטורי מרוחק (GitHub) |
| main branch ──tracking──→ origin/main | push ← pull               |

### בדיקת סביבה מוקדמת

הרץ את הפקודות הבאות כדי לוודא שאתה מוכן:

```bash
git --version
```

**צפוי:** `git version 2.x.x` או גבוה יותר

```bash
pwd
```

**צפוי:** הנתיב של התיקיה הנוכחית

```bash
ls
```

**צפוי:** תוכן התיקיה הנוכחית

**אם פקודה נכשלת:** התקן Git מ-[git-scm.com](https://git-scm.com) או פנה למנהל המערכת שלך.

---

## חלק 1: יצירת תיקיית פרויקט ואתחול ריפוזיטורי מקומי

### שלב 1.1: יצירת תיקיית פרויקט

**פקודות:**

```bash
mkdir git-team-lab
cd git-team-lab
```

**מה זה עושה:**

* `mkdir git-team-lab`: יוצר תיקיה בשם "git-team-lab"
* `cd git-team-lab`: נכנס לתיקיה החדשה

**למה זה צריך:**

* Git מבוסס תיקיות; צריך תיקיה ייעודית לפרויקט
* מארגן את העבודה ומבודד אותה מפרויקטים אחרים

**אם מדלגים:**

* Git יאתחל בתיקיה הלא נכונה (למשל הביתית)
* סיכון לערבוב קבצים

**בדיקה:**

```bash
pwd
```

* צריך להסתיים ב-`/git-team-lab`

```bash
ls -a
```

* צריך להראות רק `.` ו-`..`
* אין `.git` עדיין

---

### שלב 1.2: אתחול ריפוזיטורי Git

**פקודה:**

```bash
git init
```

**מה זה עושה:**

* יוצר תיקיה נסתרת `.git`
* מאתחל את בסיס הנתונים של Git
* הופך את התיקיה הזו לריפוזיטורי

**למה זה צריך:**

* בלי `.git`, הפקודות של Git לא יעבדו
* `.git` מכילה את כל ההיסטוריה, הסניפים, וההגדרות

**מה יש בתוך `.git`:**

* `objects/`: כל הקבצים והקומיטים
* `refs/heads/`: מצביעי סניפים (`main`, `dev`)
* `HEAD`: מצביע לסניף הנוכחי
* `config`: הגדרות ריפוזיטורי
* `index`: אזור staging

**אם מדלגים:** כל פקודת Git תיכשל

**בדיקה:**

```bash
ls -a
git status
```

* צריך לראות `.git`
* מצב: `On branch main`
* "No commits yet"

---

### שלב 1.3: יצירת קבצים בסיסיים

**פקודות:**

```bash
cat > README.md << 'EOF'
# Git Team Lab
פרויקט זה מדגים זרימת עבודה מקצועית ב-Git עם ריפוזיטוריות מרוחקות.

## Team Workflow
- `main`: קוד יציב ומוכן לפרודקשן
- `dev`: סניף אינטגרציה לבדיקות
- `feature/*`: סניפים אישיים לפיצ'רים

## Setup
ראו את הוראות המעבדה למדריך מלא
EOF
```

```bash
cat > .gitignore << 'EOF'
# macOS system files
.DS_Store
.AppleDouble
.LSOverride

# Node.js dependencies
node_modules/
npm-debug.log*

# Environment variables (NEVER commit secrets)
.env
.env.local
.env.*.local

# IDE settings
.vscode/
.idea/
*.swp
*.swo

# Build outputs
dist/
build/
*.log
EOF
```

**מה זה עושה:**

* יוצר `README.md` עם תיאור הפרויקט
* יוצר `.gitignore` כדי למנוע מעקב אחר קבצים לא רצויים

**למה זה צריך:**

* מסמכים (`README.md`)
* מניעת טעויות אבטחה או גודל (`.env`, `node_modules/`)
* מניעת קבצי מערכת או build

**בדיקה:**

```bash
cat .gitignore
ls -a
```

* צריך לראות: `.git`, `README.md`, `.gitignore`

---

### שלב 1.4: קומיט ראשון

**פקודות:**

```bash
git add .
git status
git commit -m "Initial commit: Add README and gitignore"
git log --oneline --decorate --graph -n 5
```

* `git add .`: מעביר את כל הקבצים ל-staging
* `git commit`: יוצר snapshot של הקבצים
* `git log`: מציג את הקומיט עם גרף

**בדיקה:**

* `git status`: "working tree clean"
* גרף הקומיטים מראה את ה-`HEAD` על `main`

---

## חלק 2: יצירת ריפוזיטורי מרוחק וקישור לריפוזיטורי מקומי

### שלב 2.1: יצירת ריפוזיטורי מרוחק

**GitHub:**

1. התחבר ל-[github.com](https://github.com)
2. `+` → "New repository"
3. שם: `git-team-lab`
4. **אל תסמן** "Initialize with README"
5. אל תוסיף `.gitignore` או License
6. צור ריפוזיטורי והעתק URL (HTTPS או SSH)

---

### שלב 2.2: הוספת המרוחק לריפוזיטורי מקומי

```bash
git remote add origin <REMOTE_URL>
git remote -v
```

* מוסיף מרוחק בשם "origin"
* URL נשמר בקובץ `.git/config`
* לא מעביר נתונים עדיין

---

### שלב 2.3: ווידוא שם סניף ברירת מחדל

```bash
git branch
git branch -M main  # אם יש master
```

---

### שלב 2.4: Push ראשון והגדרת Upstream

```bash
git push -u origin main
git branch -vv
git branch -r
git log --oneline --decorate --graph -n 5
```

* `-u` מגדיר מעקב בין `main` המקומי ל-`origin/main`

---

### שלב 2.5: ווידוא ב-GitHub

* פתח את הדפדפן ל-URL של הריפוזיטורי
* בדוק את `README.md`, `.gitignore`, הקומיט הראשון והסניף `main`

---

## חלק 3: בדיקות סננות מקצועיות (Sanity Checks)

**בדיקות חשובות לפני קידוד:**

1. בדוק זהות Git:

```bash
git config --global user.name
git config --global user.email
```

2. ודא URL מרוחק נכון:

```bash
git remote -v
```

3. ודא קיים Upstream Tracking:

```bash
git branch -vv
```

4. עבודה נקייה:

```bash
git status
```

5. סטטוס Ahead/Behind:

```bash
git status -sb
```

---

## חלק 4: אסטרטגיית סניפים בצוות (main/dev/feature/*)

| סניף      | תפקיד            | מי ממזג אליו             | יציבות  |
| --------- | ---------------- | ------------------------ | ------- |
| main      | קוד פרודקשן      | Release manager (מ-dev)  | גבוהה   |
| dev       | אינטגרציה/בדיקות | Developers (מ-feature/*) | בינונית |
| feature/* | משימות אישיות    | Developer                | נמוכה   |

**חוקים:**

1. לא לקמוט ישירות ל-main
2. תמיד ליצור סניף מ-dev
3. תמיד למשוך dev לפני יצירת feature branch
4. לבדוק מה ימוזג לפני המיזוג
5. למחוק סניפים מאוחדים

---

### שלב 4.1: יצירת `dev` ודחיפה

```bash
git checkout -b dev
git push -u origin dev
git branch -vv
git branch -r
```

---

### שלב 4.2: יצירת סניף feature נכון

```bash
git checkout dev
git pull
git checkout -b feature/add-installation-guide
git status
```

---

### שלב 4.3: הוספת שינוי וקומיט

```bash
cat >> README.md << 'EOF'
## Installation
1. Clone repository:
```

git clone <REMOTE_URL>
cd git-team-lab

```
2. Install dependencies:
```

npm install

```
3. Start feature branch:
```

git checkout dev
git pull
git checkout -b feature/your-feature-name

```
EOF

git add README.md
git commit -m "Add installation guide to README"
git log --oneline --decorate --graph --all -n 5
```

---

### שלב 4.4: דחיפת סניף feature

```bash
git push -u origin feature/add-installation-guide
git branch -vv
git branch -r
```

---

### שלב 4.5: מיזוג Feature → Dev

```bash
git checkout dev
git pull
git log --oneline --decorate --graph --all -n 20
git diff dev..feature/add-installation-guide
git merge feature/add-installation-guide
git status
git log --oneline --decorate --graph --all -n 10
git push
```

---

### שלב 4.6: מיזוג Dev → Main (Release)

```bash
git checkout main
git pull
git diff main..dev
git merge dev
git push
git log --oneline --decorate --graph --all -n 10
```

---

### שלב 4.7: ניקוי סניף feature

```bash
git branch -d feature/add-installation-guide
git push origin --delete feature/add-installation-guide
```

---

## חלק 5: Debugging & Checklist למניעת הפתעות

* `git branch` – על איזה סניף אתה
* `git remote -v` – URL מרוחק
* `git branch -vv` – Upstream Tracking
* `git status` – קבצים לא מקומיטים
* `git log --oneline --decorate -n 10` – היסטוריה
* `git status -sb` – Ahead/Behind

---

## חלק 6: תקלות נפוצות ופתרונות

### בעיה 1: No configured push destination

```bash
git push -u origin <branch-name>
```

### בעיה 2: rejected (non-fast-forward)

```bash
git pull
git push
# או
git pull --rebase
git push
```

### בעיה 3: לא רואים קוד ב-GitHub

* בדוק סניף, מרוחק, URL, UI של GitHub

---

## חלק 7: בדיקה סופית / Submission

**פקודות להוכחה:**

```bash
ls -a
git status
git remote -v
git branch -vv
git branch -r
git log --oneline --decorate --graph --all -n 30
```

---

## סיכום: לקחים עיקריים

* ריפוזיטורי מקומי עצמאי מהמרוחק
* Upstream tracking חשוב
* תמיד לוודא לפני משימות
* פקודות חיוניות: `git remote -v`, `git branch -vv`, `git status -sb`, `git log --graph --all`
* חוקים: לא commit ל-main, משוך לפני יצירת feature, בדוק לפני merge

---

## צעדים הבאים

* תרגול: יצירת 3 סניפים נוספים, מיזוג ל-dev, שחרור ל-main
* נושאים מתקדמים: Pull requests, Rebasing, Git hooks, פתרון קונפליקטים, Cherry-pick, Rebase אינטראקטיבי

**מקורות:**

* [Pro Git Book](https://git-scm.com/book/en/v2)
* [GitHub Guides](https://guides.github.com/)
* [GitLab Documentation](https://docs.gitlab.com/)

---

**סוף המעבדה**

---


