# 3. การจัดการและบริหาร Source Code เบื้องต้น ส่วนที่ 2

## 1. วิธีคิดและวัตถุประสงค์ของการใช้งาน Branch

1. แยกการทำงาน (Isolation)

   - แต่ละ branch สามารถแยกการทำงานออกจากกัน เช่น การพัฒนา feature ใหม่, แก้ bug, ทดสอบโค้ด หรือ refactor
   - ช่วยให้โค้ดใน branch หลัก (เช่น main) ไม่ถูกรบกวนจากการพัฒนาอื่น

2. ทำงานร่วมกันแบบขนาน (Parallel Development)

   - นักพัฒนาหลายคนสามารถพัฒนา feature คนละสาขาได้พร้อมกัน โดยไม่ชนกัน
   - ลดปัญหา merge conflict และช่วยให้ทีมทำงานได้รวดเร็วขึ้น

---

## 2. การใช้งาน Branch

```mermaid
---
config:
  gitGraph:
    parallelCommits: false
---
gitGraph:
    commit id: "Initial commit"
    commit id: "Add project setup"
    commit id: "Create basic structure"

    branch develop
    checkout develop
    commit id: "Setup development environment"
    commit id: "Add configuration files"

    branch feature/shopping-cart
    checkout feature/shopping-cart
    commit id: "Create cart component"
    commit id: "Add item management"
    commit id: "Implement cart logic"

    checkout develop
    branch feature/payment-system
    checkout feature/payment-system
    commit id: "Add payment gateway"
    commit id: "Implement checkout flow"

    checkout develop
    merge feature/shopping-cart
    commit id: "Update cart documentation"

    branch feature/user-profile
    checkout feature/user-profile
    commit id: "Create user dashboard"
    commit id: "Add profile editing"

    checkout main
    branch hotfix/critical-bug
    checkout hotfix/critical-bug
    commit id: "Fix data validation bug"
    commit id: "Add error handling"

    checkout main
    merge hotfix/critical-bug
    commit id: "Hotfix v1.0.1"

    checkout develop
    merge hotfix/critical-bug
    merge feature/payment-system
    commit id: "Resolve merge conflicts"

    merge feature/user-profile
    commit id: "Integration testing"
    commit id: "Performance optimizations"

    branch release/v1.1.0
    checkout release/v1.1.0
    commit id: "Bump version to 1.1.0"
    commit id: "Update changelog"
    commit id: "Final testing"

    checkout main
    merge release/v1.1.0
    commit id: "Release v1.1.0"

    checkout develop
    merge release/v1.1.0
    commit id: "Start next development cycle"

    branch feature/analytics
    checkout feature/analytics
    commit id: "Add tracking events"
    commit id: "Implement analytics dashboard"
```

- การสร้าง branch

  ```sh
  git branch <branch-name>
  ```

- การลบ branch

  ```sh
  git branch -D <branch-name>
  git branch --delete branch-name
  ```

- การสลับ branch

  ```sh
  git-switch <branch-name>
  ```

- การสร้างและสลับ branch

  ```sh
  git-switch -c <branch-name>
  ```

---

## 3. การ merge branch

### git merge

```mermaid
gitGraph:
    commit id: "Start project"
    commit id: "Add homepage"

    branch feature
    checkout feature
    commit id: "Create new feature"
    commit id: "Fix bug in feature"

    checkout main
    commit id: "Update README"

    merge feature
    commit id: "Deploy to production"
```

```sh
git merge <branch>
```

### git rebase

#### Before Rebase

```mermaid
gitGraph:
    commit id: "A: Initial"
    commit id: "B: Add feature"

    branch feature
    checkout feature
    commit id: "C: Work on feature"
    commit id: "D: Fix feature bug"

    checkout main
    commit id: "E: Update docs"
    commit id: "F: Fix main bug"
```

#### After Rebase

```mermaid
gitGraph:
    commit id: "A: Initial"
    commit id: "B: Add feature"
    commit id: "E: Update docs"
    commit id: "F: Fix main bug"
    commit id: "C': Work on feature"
    commit id: "D': Fix feature bug"
```

```sh
git rebase <branch name>
```

---

## 4. การใช้งาน Tags

![tag](./images/15-tag.png)

```sh
git tag
git tag -l
```

```sh
git tag
git tag -a <tagname> -m <message>
git tag -a <tagname> <commit ID>
```

```sh
git tag -d <tagname>
```

```sh
git show <tagname>
```

```sh
git push <remote branch> <tagname>
git push <remote branch> --tags
```

```sh
git push origin --delete <tagname>
```

```sh
git checkout <tagname>
```

---

## 5. commands อื่นๆ

[image]

- git revert
- git filter

---

## 6. In Action

1. Check current branch

   ```sh
   git branch
   ```

   ![branch](./images/01-branch.png)

2. Create new branch

   ```sh
   git branch dev
   ```

   ![create branch](./images/02-create-branch.png)

3. Switch branch

   ```sh
   git switch dev
   ```

   ![03-switch-branch](./images/03-switch-branch.png)

4. Modify and Commit on `dev`

   ```sh
   echo “On branch dev” > README
   git commit -am “Add new content on branch dev”
   ```

   ![04-modify-dev](./images/04-modify-dev.png)

5. Switch to `main`

   ```sh
   git switch main
   ```

   ![05-switch-main](./images/05-switch-main.png)

6. Modified and commit on `main`

   ```sh
   echo “On branch main” >> README
   git commit -am “Add new content on branch main”
   ```

   ![06-modify-main](./images/06-modify-main.png)

7. Switch to `dev`

   ```sh
   git switch dev
   ```

8. Merge branch `main` to `dev`

   ```sh
   git merge main
   ```

   ![07-merge-main-to-dev](./images/07-merge-main-to-dev.png)
   ![08-merge-conflict](./images/08-merge-conflict.png)

9. Check status

   ```sh
   git status
   ```

   ![09-status](./images/09-status.png)

10. Conflict

    ```sh
    cat README
    ```

    ![10-readme](./images/10-readme.png)

11. Fix Conflict

    ```sh
    git status
    git add README
    git commit
    ```

12. Switch to main

    ```sh
    git switch main
    ```

13. Merge branch `dev` to `master`

    ```sh
    git merge dev
    ```

    ![11-merge-dev-to-main](./images/11-merge-dev-to-main.png)

---

## 7. Avoid Merge Conflict

1. Small change and commit
2. Early merge
3. Single Responsibility Principle
4. Communication is a Key
5. Mob programming

---

## 8. Rebase

1. Log and copy commit id

   ```sh
   git log --oneline --graph --all
   ```

   - copy dev commit ID(before merge)
   - copy main commit ID(before merge)

2. Reset to before merge: `dev`, `main`

   ```sh
   # main
   git switch main
   git reset --hard <commit ID>

   # dev
   git switch dev
   git reset --hard <commit ID>
   ```

   ![hard reset](./images/12-hard-reset.png)

3. Rebase

   ```sh
   git switch dev
   git rebase main
   ```

   ![rebase](./images/13-rebase.png)

   ![rebase status](./images/14-rebase.png)

---

[home](../../README.md#) | [current](../../README.md#3-การจัดการและบริหาร-source-code-เบื้องต้น-ส่วนที่-2) | [next](../../README.md#4-การจัดการและบริหาร-source-code-โดยใช้การแชร์ผ่านเครื่อง-remote-server-ส่วนที่-1)
