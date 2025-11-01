# 🧩 Optimizing User, Group, and Role Management with Access Control and Workflows

**Author:** Balakumaran K  
**College:** University College of Engineering, Ariyalur  
**Date:** November 2, 2025

---

## 🎯 Project Overview

This ServiceNow project implements a **role-based access control (RBAC)** system and an **automated workflow** for a project management scenario.

It defines two users:
- **Alice** – Project Manager (PM)
- **Bob** – Team Member (TM)

The system ensures:
- Only authorized users can edit specific fields
- Task approvals are automated via **Flow Designer**
- Accountability and progress tracking are enforced through structured access control

---

## 🧠 Problem Statement

> In a small project management team consisting of a Project Manager (Alice) and a Team Member (Bob), there is a need to efficiently manage project tasks and ensure accountability throughout the project lifecycle. The current system lacks clear role definitions, access controls, and a structured workflow, leading to confusion regarding task assignments and progress tracking.

---

## ⚙️ Objectives

- Define and enforce **clear user roles**
- Create a **custom task management table** with role-based permissions
- Automate approval workflow using **Flow Designer**
- Validate user restrictions through **Access Control Lists (ACLs)**

---

## 🧩 System Architecture

### 👤 Roles

| Role | Description | User |
|------|-------------|------|
| `project_member` | Full CRUD access to all tasks | Alice |
| `team_member` | Restricted to editing only Status and Comment | Bob |

### 🗄️ Database Table — `u_task_table_2`

| Field | Type | Description |
|-------|------|-------------|
| Task Name (`u_task_name`) | String | Title of the task |
| Description (`u_description`) | String (Long) | Details about the task |
| Assigned To (`assigned_to`) | Reference → User | Assigned user |
| Status (`u_status`) | Choice | Task state (new, in_progress, completed) |
| Comment (`u_comment`) | String/Journal | Feedback or progress note |
| Priority (`u_priority`) | Choice | low, medium, high |
| Due Date (`u_due_date`) | Date | Task deadline |

---

## 🧱 Access Control (ACL Configuration)

| Field | Operation | Role | Permission |
|-------|-----------|------|------------|
| Entire table (`u_task_table_2.*`) | CRUD | `project_member` | Full |
| Status (`u_status`) | Write | `team_member` | Editable |
| Comment (`u_comment`) | Write | `team_member` | Editable |
| Other Fields | Write | `team_member` | ❌ Restricted |

> ✅ **Result:**  
> - Bob can edit only *Status* and *Comment*
> - Alice has full access (create, update, delete)

---

## ⚙️ Workflow — Flow Designer Setup

### 🧭 Flow: `Task Table`

- **Application:** Global
- **Trigger:** Record Created or Updated
- **Table:** `u_task_table_2`

#### Trigger Conditions:

| Field | Operator | Value |
|-------|----------|-------|
| `u_status` | is | `in_progress` |
| `u_comment` | is | `feedback` |
| `assigned_to` | is | `bob.p` |

#### Actions:

1. **Update Record**
   - Field: `u_status`
   - Value: `completed`

2. **Ask for Approval**
   - Approver: `Alice`
   - Approval field: `u_status`
   - Waits until approval is received

> 🧩 After approval, the flow automatically updates the task's status and logs the action in the **Flow Executions** list.

---

## 🧪 Performance Testing

| Test ID | Description | Expected Result | Status |
|---------|-------------|-----------------|--------|
| T1 | Bob updates task status to *In Progress* | Flow triggers update → `Completed` | ✅ Pass |
| T2 | Alice approves the task | Approval recorded, task finalized | ✅ Pass |
| T3 | Bob tries to edit restricted fields | Access Denied | ✅ Pass |

**Results Summary:**
- ACL enforcement successful
- Workflow automation works as expected
- Approval notifications visible under *My Approvals* for Alice
- Flow logs confirm successful executions

---

## 📋 Requirement Analysis

### Functional Requirements

- Alice creates and manages all tasks
- Bob can view all tasks but edit only his assigned ones
- Approval flow notifies Alice when Bob completes a task

### Non-Functional Requirements

| Category | Requirement |
|----------|-------------|
| Platform | ServiceNow PDI |
| Security | Access Control Lists (ACL) |
| Reliability | Flow Designer Execution Validation |
| Maintainability | Modular role and table structure |

---

## 🧩 Project Design Phase

### 🏗️ System Layers

```
Admin → Configuration
  ↓
Alice (PM) → Task Management + Approval
  ↓
Bob (TM) → Status / Comment updates
```

### 🔄 Workflow Logic

```
Trigger: Task updated → Status = in_progress
  ↓
Action 1: Update record → Status = completed
  ↓
Action 2: Ask for approval → Approver = Alice
  ↓
Alice approves → Task finalized
```

---

## 📅 Project Planning Timeline

| Week | Task | Description |
|------|------|-------------|
| Week 1 | Setup | PDI instance setup, users, and roles |
| Week 2 | Roles | Assign `project_member` and `team_member` roles |
| Week 3 | Tables | Create `u_task_table_2` and define fields |
| Week 4 | Workflow | Build Flow Designer logic and test |

---

## 🧠 Ideation Phase Summary

| Phase | Description |
|-------|-------------|
| Problem Definition | Lack of access-based workflows in project management |
| Brainstorming | Introduced ServiceNow ACL + Flow Designer automation |
| Prioritization | Focused on role clarity, automation, and audit tracking |

---

## 🧩 Requirement Matrix

| User | Task | Create | Read | Update | Delete | Approve |
|------|------|--------|------|--------|--------|---------|
| Alice (PM) | All Tasks | ✅ | ✅ | ✅ | ✅ | ✅ |
| Bob (TM) | Assigned Tasks | ❌ | ✅ | ✅ (Status & Comment only) | ❌ | ❌ |

---

## ⚡ Test Case Summary

| Scenario | Expected Output | Result |
|----------|----------------|--------|
| Bob edits Status | Updated successfully | ✅ |
| Bob edits Description | Access denied | ✅ |
| Alice edits all fields | Allowed | ✅ |
| Alice approval | Task completes | ✅ |

---

## 📸 Screenshots to Include

1. **Users list** — Alice and Bob with assigned roles
2. **Alice's user record** — showing `project_member`
3. **Bob's user record** — showing `team_member`
4. **Task table (`u_task_table_2`)** — field configuration
5. **ACL list** — showing `u_status` and `u_comment` roles
6. **Flow Designer canvas** — trigger, update, approval actions
7. **Flow Executions tab** — status = Completed
8. **Bob's task view** — restricted edit fields visible
9. **Alice's approval page** — approval request
10. **Final task state** — `Status: Completed`

---

## 📚 Deliverables

- 📄 `01_Ideation_Phase/` – problem statement, brainstorming, feasibility
- 🧭 `02_Planning_Phase/` – scope, tools, and project timeline
- 🧱 `03_Design_Phase/` – architecture and workflow design
- 📋 `04_Requirements/` – functional and non-functional analysis
- 🧪 `05_Performance_Testing/` – test results and screenshots
- 🧾 `README.md` – final documentation

---

## ✅ Conclusion

This ServiceNow-based project successfully demonstrates **role-based access control** and **workflow automation** within a project management context.

- Bob (Team Member) can update only task progress and feedback
- Alice (Project Manager) reviews and approves changes
- Flow Designer ensures tasks are automatically marked *Completed* after approval
- ACLs enforce data integrity and prevent unauthorized modifications

**Outcome:**  
Efficient task management, automated approvals, and traceable project accountability.

---

## 🧭 Useful References

| Module | Navigation Path | Purpose |
|--------|----------------|---------|
| Users | `sys_user.list` | Manage user accounts |
| Roles | `sys_user_role.list` | Create / assign roles |
| ACLs | `sys_security_acl.list` | Configure access control |
| Flow Designer | `Flow Designer` → `Executions` | Manage and monitor flows |

---

### 🏁 Final Note

> "Access control isn't about restriction — it's about clarity. This project ensures every action in the system has a rightful owner."

**Developed by:**  
**Balakumaran K**  
University College of Engineering, Ariyalur  
*(Project Submission — Nov 2025)*
