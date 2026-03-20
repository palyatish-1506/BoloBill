/**
 * BoloBill — Backend Server (server.js)
 * Node.js + Express REST API
 * Atmiya University Hackathon Project
 * Shree Krishna Dhaba, Rajkot, Gujarat
 *
 * Setup:
 *   npm install express cors body-parser uuid
 *   node server.js
 *
 * API runs on: http://localhost:3000
 */

const express    = require('express');
const cors       = require('cors');
const bodyParser = require('body-parser');
const { v4: uuidv4 } = require('uuid');

const app  = express();
const PORT = process.env.PORT || 3000;

// ─── Middleware ────────────────────────────────────────────────────────────────
app.use(cors());
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));

// ─── In-Memory Store (replace with PostgreSQL / Firebase in production) ────────
let restaurantProfile = {
  name        : 'Shree Krishna Dhaba',
  address     : 'Rajkot, Gujarat',
  gstin       : '24AAAAA0000A1Z5',
  phone       : '+91 98765 43210',
  upiId       : 'shreekrish@upi',
  logoUrl     : '',
  currency    : 'INR',
  cgstRate    : 2.5,   // %
  sgstRate    : 2.5,   // %
};

let menuItems   = [];   // Array of menu item objects
let orders      = {};   // key: orderId, value: order object
let billHistory = [];   // Array of completed bills
let staff       = [
  { id: 'mgr-1',  role: 'manager', name: 'Admin',         pin: '1234', active: true },
  { id: 'wtr-1',  role: 'waiter',  name: 'Ramesh Patel',  pin: '2345', active: true },
  { id: 'wtr-2',  role: 'waiter',  name: 'Suresh Kumar',  pin: '3456', active: true },
  { id: 'wtr-3',  role: 'waiter',  name: 'Priya Shah',    pin: '4567', active: true },
];

let failedPinAttempts = {}; // key: staffId, value: { count, lockedUntil }

// ─── Helper: timestamp ─────────────────────────────────────────────────────────
const now = () => new Date().toISOString();

// ─── Helper: compute bill totals ───────────────────────────────────────────────
function computeTotals(items) {
  const subtotal = items.reduce((sum, i) => sum + i.price * i.qty, 0);
  const cgst     = parseFloat(((subtotal * restaurantProfile.cgstRate) / 100).toFixed(2));
  const sgst     = parseFloat(((subtotal * restaurantProfile.sgstRate) / 100).toFixed(2));
  const total    = parseFloat((subtotal + cgst + sgst).toFixed(2));
  return { subtotal, cgst, sgst, total };
}

// ══════════════════════════════════════════════════════════════════════════════
// ROUTES
// ══════════════════════════════════════════════════════════════════════════════

// ─── Root ──────────────────────────────────────────────────────────────────────
app.get('/', (req, res) => {
  res.json({
    app     : 'BoloBill API',
    version : '1.0.0',
    status  : 'running',
    time    : now(),
  });
});

// ══════════════════════════════════════════════════════════════════════════════
// AUTH
// ══════════════════════════════════════════════════════════════════════════════

/**
 * POST /api/auth/login
 * Body: { name: string, pin: string }
 * Returns staff record (without PIN) + role
 */
app.post('/api/auth/login', (req, res) => {
  const { name, pin } = req.body;

  if (!name || !pin) {
    return res.status(400).json({ error: 'Name and PIN are required.' });
  }

  const member = staff.find(
    s => s.name.toLowerCase() === name.toLowerCase() && s.active
  );

  if (!member) {
    return res.status(401).json({ error: 'Staff member not found.' });
  }

  // Lockout check
  const attempt = failedPinAttempts[member.id] || { count: 0, lockedUntil: 0 };
  if (Date.now() < attempt.lockedUntil) {
    const secsLeft = Math.ceil((attempt.lockedUntil - Date.now()) / 1000);
    return res.status(403).json({
      error      : `Account locked. Try again in ${secsLeft}s.`,
      lockedUntil: attempt.lockedUntil,
    });
  }

  if (member.pin !== pin) {
    attempt.count += 1;
    if (attempt.count >= 5) {
      attempt.lockedUntil = Date.now() + 30_000; // 30-second lockout
      attempt.count = 0;
    }
    failedPinAttempts[member.id] = attempt;
    return res.status(401).json({ error: 'Incorrect PIN.', attempts: attempt.count });
  }

  // Success — reset failed attempts
  failedPinAttempts[member.id] = { count: 0, lockedUntil: 0 };

  const { pin: _hidden, ...safeRecord } = member;
  res.json({ success: true, staff: safeRecord });
});

// ══════════════════════════════════════════════════════════════════════════════
// RESTAURANT PROFILE
// ══════════════════════════════════════════════════════════════════════════════

/** GET /api/profile */
app.get('/api/profile', (req, res) => {
  res.json(restaurantProfile);
});

/** PUT /api/profile  — Manager only (no auth middleware for demo) */
app.put('/api/profile', (req, res) => {
  const allowed = [
    'name','address','gstin','phone','upiId','logoUrl',
    'currency','cgstRate','sgstRate',
  ];
  allowed.forEach(k => {
    if (req.body[k] !== undefined) restaurantProfile[k] = req.body[k];
  });
  res.json({ success: true, profile: restaurantProfile });
});

// ══════════════════════════════════════════════════════════════════════════════
// MENU
// ══════════════════════════════════════════════════════════════════════════════

/** GET /api/menu */
app.get('/api/menu', (req, res) => {
  res.json(menuItems);
});

/** POST /api/menu  — Add item */
app.post('/api/menu', (req, res) => {
  const { name, category, price, hsnCode, available } = req.body;
  if (!name || price === undefined) {
    return res.status(400).json({ error: 'name and price are required.' });
  }
  const item = {
    id       : uuidv4(),
    name,
    category : category || 'Main Course',
    price    : parseFloat(price),
    hsnCode  : hsnCode  || '9963',
    available: available !== false,
    createdAt: now(),
  };
  menuItems.push(item);
  res.status(201).json(item);
});

/** PUT /api/menu/:id  — Update item */
app.put('/api/menu/:id', (req, res) => {
  const idx = menuItems.findIndex(m => m.id === req.params.id);
  if (idx === -1) return res.status(404).json({ error: 'Item not found.' });
  menuItems[idx] = { ...menuItems[idx], ...req.body, id: req.params.id };
  res.json(menuItems[idx]);
});

/** DELETE /api/menu/:id */
app.delete('/api/menu/:id', (req, res) => {
  menuItems = menuItems.filter(m => m.id !== req.params.id);
  res.json({ success: true });
});

// ══════════════════════════════════════════════════════════════════════════════
// ORDERS
// ══════════════════════════════════════════════════════════════════════════════

/** GET /api/orders  — All pending orders */
app.get('/api/orders', (req, res) => {
  res.json(Object.values(orders));
});

/** POST /api/orders  — Place a new order (from waiter voice or customer self-order) */
app.post('/api/orders', (req, res) => {
  const { tableNo, customerName, customerPhone, waiterId, items } = req.body;
  if (!tableNo || !items || !items.length) {
    return res.status(400).json({ error: 'tableNo and items[] are required.' });
  }

  const orderId = uuidv4();
  const totals  = computeTotals(items);

  const order = {
    orderId,
    tableNo,
    customerName : customerName || 'Guest',
    customerPhone: customerPhone || '',
    waiterId     : waiterId || '',
    items,
    status       : 'pending',   // pending | accepted | served | paid | cancelled
    paymentMethod: null,
    ...totals,
    createdAt    : now(),
    updatedAt    : now(),
  };

  orders[orderId] = order;
  res.status(201).json(order);
});

/** GET /api/orders/:id */
app.get('/api/orders/:id', (req, res) => {
  const order = orders[req.params.id];
  if (!order) return res.status(404).json({ error: 'Order not found.' });
  res.json(order);
});

/** PATCH /api/orders/:id/status  — Update order status */
app.patch('/api/orders/:id/status', (req, res) => {
  const order = orders[req.params.id];
  if (!order) return res.status(404).json({ error: 'Order not found.' });

  const { status, paymentMethod } = req.body;
  const validStatuses = ['pending','accepted','served','paid','cancelled'];
  if (!validStatuses.includes(status)) {
    return res.status(400).json({ error: `Status must be one of: ${validStatuses.join(', ')}` });
  }

  order.status    = status;
  order.updatedAt = now();
  if (paymentMethod) order.paymentMethod = paymentMethod;

  // Move to history when paid
  if (status === 'paid') {
    billHistory.unshift({ ...order, paidAt: now() });
    delete orders[order.orderId];
  }

  res.json(order);
});

/** DELETE /api/orders/:id  — Cancel / remove */
app.delete('/api/orders/:id', (req, res) => {
  if (!orders[req.params.id]) {
    return res.status(404).json({ error: 'Order not found.' });
  }
  delete orders[req.params.id];
  res.json({ success: true });
});

// ══════════════════════════════════════════════════════════════════════════════
// BILL / HISTORY
// ══════════════════════════════════════════════════════════════════════════════

/** GET /api/history  — All paid bills */
app.get('/api/history', (req, res) => {
  res.json(billHistory);
});

/** GET /api/history/export  — CSV download */
app.get('/api/history/export', (req, res) => {
  const header = 'Bill ID,Table,Customer,Phone,Items,Subtotal,CGST,SGST,Total,Payment,Paid At\n';
  const rows = billHistory.map(b => {
    const itemStr = b.items.map(i => `${i.qty}x ${i.name}`).join(' | ');
    return [
      b.orderId, b.tableNo, b.customerName, b.customerPhone,
      `"${itemStr}"`, b.subtotal, b.cgst, b.sgst, b.total,
      b.paymentMethod || '', b.paidAt || '',
    ].join(',');
  });
  res.setHeader('Content-Type', 'text/csv');
  res.setHeader('Content-Disposition', 'attachment; filename="bolobill-history.csv"');
  res.send(header + rows.join('\n'));
});

// ══════════════════════════════════════════════════════════════════════════════
// STAFF MANAGEMENT
// ══════════════════════════════════════════════════════════════════════════════

/** GET /api/staff  — List all (PINs hidden) */
app.get('/api/staff', (req, res) => {
  res.json(staff.map(({ pin: _, ...s }) => s));
});

/** POST /api/staff  — Add staff member */
app.post('/api/staff', (req, res) => {
  const { name, role, pin } = req.body;
  if (!name || !role || !pin) {
    return res.status(400).json({ error: 'name, role and pin are required.' });
  }
  const member = {
    id    : uuidv4(),
    role  : ['manager','waiter'].includes(role) ? role : 'waiter',
    name,
    pin,
    active: true,
  };
  staff.push(member);
  const { pin: _, ...safe } = member;
  res.status(201).json(safe);
});

/** DELETE /api/staff/:id */
app.delete('/api/staff/:id', (req, res) => {
  const idx = staff.findIndex(s => s.id === req.params.id);
  if (idx === -1) return res.status(404).json({ error: 'Staff not found.' });
  staff.splice(idx, 1);
  res.json({ success: true });
});

// ══════════════════════════════════════════════════════════════════════════════
// DASHBOARD STATS
// ══════════════════════════════════════════════════════════════════════════════

/** GET /api/stats  — Quick dashboard summary */
app.get('/api/stats', (req, res) => {
  const today      = new Date().toDateString();
  const todayBills = billHistory.filter(b => new Date(b.paidAt).toDateString() === today);
  const revenue    = todayBills.reduce((s, b) => s + b.total, 0);

  res.json({
    pendingOrders : Object.keys(orders).length,
    todayBills    : todayBills.length,
    todayRevenue  : parseFloat(revenue.toFixed(2)),
    menuItems     : menuItems.length,
    staffCount    : staff.filter(s => s.active).length,
  });
});

// ─── 404 fallback ─────────────────────────────────────────────────────────────
app.use((req, res) => {
  res.status(404).json({ error: 'Endpoint not found.' });
});

// ─── Start server ─────────────────────────────────────────────────────────────
app.listen(PORT, () => {
  console.log(`\n🍽️  BoloBill API running on http://localhost:${PORT}`);
  console.log('   Press Ctrl+C to stop.\n');
});

module.exports = app;
