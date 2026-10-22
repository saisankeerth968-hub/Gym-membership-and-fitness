# Gym Membership and Fitness Session Management System

DBMS Project-Based Learning (PBL) project — Woxsen University, B.Tech CSE AIML (2026–27)

A relational database (MySQL) with a small Python (Streamlit) front end to manage gym members, subscriptions, trainers, assessments, classes, bookings, attendance, equipment maintenance, payments and renewals. Business rules are enforced inside the database using constraints and triggers.

**Author:** P.Sankeerth reddy(25WU0102193)

## Features

- Member, plan, subscription, payment and renewal management
- Trainer assignment, fitness assessments and workout programmes
- Class scheduling, booking and attendance
- Equipment inventory and maintenance tracking
- Reports: active/expired members, class attendance, trainer load, equipment maintenance, upcoming renewals, revenue by plan

## Business rules enforced by the database

| Rule | How |
|---|---|
| Active subscription required to book a class | `BEFORE INSERT` trigger on `class_registration` |
| Class capacity cannot be exceeded | Same trigger (counts `Booked` rows) |
| No trainer overlap | Triggers on `class_schedule` (insert and update) |
| Non-negative assessment values | `CHECK` constraints on `assessment` |
| Valid renewal dates | `BEFORE INSERT` trigger on `renewal` |
| Class attendance only for booked members | Trigger on `attendance` |

## Database design

- 15 tables, normalized to 3NF (also BCNF)
- 7 views, 5 triggers, indexes on frequently searched columns
- Tables: `member`, `membership_plan`, `subscription`, `trainer`, `trainer_assignment`, `assessment`, `workout_programme`, `fitness_class`, `class_schedule`, `class_registration`, `attendance`, `equipment`, `maintenance`, `payment`, `renewal`
- Views: `v_active_members`, `v_expired_members`, `v_class_attendance`, `v_trainer_load`, `v_equipment_maintenance`, `v_upcoming_renewals`, `v_revenue_by_plan`

## Tech stack

- MySQL 8.0 (script also tested on MariaDB 10.11)
- Python 3.x
- Streamlit
- mysql-connector-python

## Setup

1. Create the database, tables, triggers, views and sample data (this drops and recreates `gym_db`):

   ```bash
   mysql -u root -p < gym_db.sql
   ```

2. Install dependencies:

   ```bash
   pip install streamlit mysql-connector-python
   ```

3. Set the database credentials as environment variables:

   ```bash
   export DB_HOST=localhost
   export DB_USER=root
   export DB_PASSWORD=your_password
   export DB_NAME=gym_db
   ```

   On Windows PowerShell use `$env:DB_USER="root"` and so on.

4. Run the app:

   ```bash
   streamlit run app.py
   ```

## Sample queries

```sql
SELECT * FROM v_active_members;
SELECT * FROM v_expired_members;
SELECT * FROM v_class_attendance;
SELECT * FROM v_revenue_by_plan;
```

## Testing the rules

Try these on the sample data; each should be rejected with an error:

```sql
-- Class already full (capacity 3)
INSERT INTO class_registration (schedule_id, member_id) VALUES (9, 12);

-- Member without an active subscription
INSERT INTO class_registration (schedule_id, member_id) VALUES (12, 13);

-- Negative assessment value
INSERT INTO assessment (member_id, trainer_id, assessment_date, weight_kg, height_cm, body_fat_pct, bmi, resting_heart_rate)
VALUES (1, 1, CURDATE(), -70, 175, 20, 22, 70);
```

## Project structure

```
.
├── gym_db.sql      # schema, triggers, views, sample data
├── app.py          # Streamlit application
├── README.md
└── docs/
    └── DBMS_PBL_Report.pdf
```

## Notes

All sample data is synthetic. Sample dates are relative to the current date, so active/expired members and upcoming renewals always appear.
