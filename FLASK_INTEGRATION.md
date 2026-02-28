# Flask Integration Guide

## Add PostgreSQL Support to Your Flask App

### 1. Install Dependencies

Add to `requirements.txt`:
```
psycopg2-binary==2.9.9
```

### 2. Copy Helper File

Copy `db_postgres.py` to your Flask app root directory.

### 3. Update app.py

Add at the top:
```python
import os

USE_POSTGRES = os.getenv('USE_POSTGRES', 'false').lower() == 'true'

if USE_POSTGRES:
    from db_postgres import (
        get_orders, get_order_by_id, update_order_status,
        get_products, get_users, get_dashboard_stats,
        get_revenue_by_month, get_orders_by_status
    )
```

### 4. Update Routes

**Dashboard Example:**
```python
@app.route('/')
def dashboard():
    if USE_POSTGRES:
        stats = get_dashboard_stats()
        orders = get_orders(limit=5)
        return render_template('dashboard.html',
            total_orders=stats['total_orders'],
            total_revenue=stats['total_revenue'],
            pending_orders=stats['pending_orders'],
            orders=orders
        )
    else:
        # Keep Firebase fallback
        pass
```

**Orders Example:**
```python
@app.route('/orders')
def orders():
    if USE_POSTGRES:
        page = request.args.get('page', 1, type=int)
        status = request.args.get('status')
        orders = get_orders(limit=50, offset=(page-1)*50, status=status)
        return render_template('orders.html', orders=orders)
    else:
        # Keep Firebase fallback
        pass
```

### 5. Add Environment Variables

In Render.com dashboard, add:
```
USE_POSTGRES=true
POSTGRES_HOST=containers-us-west-xxx.railway.app
POSTGRES_DB=railway
POSTGRES_USER=postgres
POSTGRES_PASSWORD=your_password
```

### 6. Deploy

```bash
git add .
git commit -m "Add PostgreSQL support"
git push
```

Render will auto-deploy.

### 7. Verify

1. Visit your dashboard
2. Check Firebase Console → Usage
3. Should see ZERO reads
4. Refresh multiple times → still ZERO reads

---

## Available Functions

```python
# Orders
get_orders(limit=50, offset=0, status=None)
get_order_by_id(order_id)
update_order_status(order_id, status)

# Products
get_products(limit=50, offset=0)

# Users
get_users(limit=50, offset=0)

# Analytics
get_dashboard_stats()
get_revenue_by_month()
get_orders_by_status()
```

---

## Testing Locally

```bash
# Set environment variables
export USE_POSTGRES=true
export POSTGRES_HOST=localhost
export POSTGRES_DB=frizzly
export POSTGRES_USER=admin
export POSTGRES_PASSWORD=frizzly2026secure

# Run Flask app
python app.py
```

---

## Fallback Strategy

Keep Firebase as fallback:
```python
if USE_POSTGRES:
    try:
        stats = get_dashboard_stats()
    except Exception as e:
        print(f"PostgreSQL error: {e}")
        # Fall back to Firebase
        stats = get_firebase_stats()
else:
    stats = get_firebase_stats()
```

This ensures your app works even if PostgreSQL is down.
