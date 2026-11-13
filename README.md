# Task Management System

A simple REST API for managing tasks, built with **Python, Django, Django REST Framework, SQLite and django-filter**.

## Project Overview

The system has one Django app (`tasks`) with 4 models:

| Model | Fields | Relationships |
|-------|--------|---------------|
| Category | name, description, created_at | One category has many tasks |
| Tag | name, created_at | Many-to-many with Task |
| Task | title, description, category, tags, status, priority, due_date, created_at, updated_at | ForeignKey to Category, ManyToMany to Tag |
| TaskDetail | task, additional_notes, reminder_date | OneToOne with Task |

Features: CRUD APIs (ModelViewSet + DefaultRouter), validation and duplicate prevention, filtering / searching / ordering on tasks, pagination (5 per page), and a customized Django Admin.

**Validation rules**
- Category and tag names must be unique (case-insensitive).
- A task title must be at least 3 characters and unique within its category.
- A new task cannot have a due date in the past.
- A task can have only one TaskDetail.

## Installation

```bash
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install -r requirements.txt
```

## Migrations and Run

```bash
python manage.py makemigrations
python manage.py migrate
python manage.py createsuperuser   # optional, for /admin/
python manage.py runserver
```

Run the tests: `python manage.py test`

- API root: http://127.0.0.1:8000/api/
- Admin: http://127.0.0.1:8000/admin/

## API Endpoints

Each endpoint supports `GET` (list), `POST` (create), `GET /{id}/`, `PUT /{id}/`, `PATCH /{id}/` and `DELETE /{id}/`.

| Endpoint | Description |
|----------|-------------|
| `/api/categories/` | Manage categories |
| `/api/tags/` | Manage tags |
| `/api/tasks/` | Manage tasks |
| `/api/task-details/` | Manage task details |

**Task query parameters**

| Purpose | Example |
|---------|---------|
| Filter by status | `/api/tasks/?status=pending` |
| Filter by priority | `/api/tasks/?priority=high` |
| Filter by category id | `/api/tasks/?category=1` |
| Filter by tag name | `/api/tasks/?tag=urgent` |
| Due date range | `/api/tasks/?due_date_after=2026-12-01&due_date_before=2026-12-31` |
| Search | `/api/tasks/?search=report` |
| Order | `/api/tasks/?ordering=due_date` or `?ordering=-priority` |
| Page | `/api/tasks/?page=2` |

Status values: `pending`, `in_progress`, `completed`. Priority values: `low`, `medium`, `high`.

## Example Requests

Create a category:
```bash
curl -X POST http://127.0.0.1:8000/api/categories/ \
  -H "Content-Type: application/json" \
  -d '{"name": "Study", "description": "University work"}'
```

Create a tag:
```bash
curl -X POST http://127.0.0.1:8000/api/tags/ \
  -H "Content-Type: application/json" \
  -d '{"name": "urgent"}'
```

Create a task (use a future due date):
```bash
curl -X POST http://127.0.0.1:8000/api/tasks/ \
  -H "Content-Type: application/json" \
  -d '{"title": "Finish final project", "description": "Complete the API", "category": 1, "tags": [1], "priority": "high", "due_date": "2026-12-20"}'
```

Add details to a task:
```bash
curl -X POST http://127.0.0.1:8000/api/task-details/ \
  -H "Content-Type: application/json" \
  -d '{"task": 1, "additional_notes": "Prepare viva slides", "reminder_date": "2026-12-18T09:00:00+06:00"}'
```

Mark a task as completed:
```bash
curl -X PATCH http://127.0.0.1:8000/api/tasks/1/ \
  -H "Content-Type: application/json" \
  -d '{"status": "completed"}'
```

Filter + search + order:
```bash
curl "http://127.0.0.1:8000/api/tasks/?status=pending&priority=high&search=project&ordering=due_date"
```

Delete a task:
```bash
curl -X DELETE http://127.0.0.1:8000/api/tasks/1/
```

## Project Structure

```
task_management_system/
├── manage.py
├── requirements.txt
├── README.md
├── config/          # project settings and root urls
└── tasks/           # models, serializers, views, filters, urls, admin, tests
```
