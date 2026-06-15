# Ecommerce-app
import { useState, useEffect } from "react";

// ─── Data ────────────────────────────────────────────────────────────────────
const PRODUCTS = [
  { id: 1, name: "Wireless Noise-Cancelling Headphones", category: "Electronics", price: 249.99, stock: 14, rating: 4.8, reviews: 312, emoji: "🎧", badge: "Bestseller" },
  { id: 2, name: "Mechanical Keyboard RGB", category: "Electronics", price: 129.99, stock: 6, rating: 4.6, reviews: 189, emoji: "⌨️", badge: "Hot" },
  { id: 3, name: "Running Shoes Pro X", category: "Footwear", price: 89.99, stock: 23, rating: 4.5, reviews: 421, emoji: "👟", badge: null },
  { id: 4, name: "Leather Minimalist Wallet", category: "Accessories", price: 39.99, stock: 50, rating: 4.7, reviews: 98, emoji: "👛", badge: "New" },
  { id: 5, name: "Stainless Steel Water Bottle", category: "Lifestyle", price: 34.99, stock: 0, rating: 4.4, reviews: 256, emoji: "🍶", badge: null },
  { id: 6, name: "Smart Watch Series 9", category: "Electronics", price: 399.99, stock: 9, rating: 4.9, reviews: 547, emoji: "⌚", badge: "Bestseller" },
  { id: 7, name: "Yoga Mat Premium", category: "Fitness", price: 59.99, stock: 31, rating: 4.3, reviews: 167, emoji: "🧘", badge: null },
  { id: 8, name: "Portable Bluetooth Speaker", category: "Electronics", price: 79.99, stock: 18, rating: 4.5, reviews: 203, emoji: "🔊", badge: "Hot" },
];

const CATEGORIES = ["All", "Electronics", "Footwear", "Accessories", "Lifestyle", "Fitness"];

const ORDERS = [
  { id: "ORD-4821", date: "2026-06-12", items: ["Wireless Headphones", "Wallet"], total: 289.98, status: "Delivered" },
  { id: "ORD-4756", date: "2026-06-08", items: ["Smart Watch Series 9"], total: 399.99, status: "Shipped" },
  { id: "ORD-4690", date: "2026-05-30", items: ["Running Shoes Pro X", "Yoga Mat"], total: 149.98, status: "Delivered" },
];

// ─── Helpers ──────────────────────────────────────────────────────────────────
const fmt = (n) => `$${Number(n).toFixed(2)}`;
const Stars = ({ r }) => (
  <span style={{ color: "#f59e0b", fontSize: 12 }}>
    {"★".repeat(Math.floor(r))}{"☆".repeat(5 - Math.floor(r))}
  </span>
);

// ─── Sub-components ───────────────────────────────────────────────────────────

function Toast({ msg, onDone }) {
  useEffect(() => { const t = setTimeout(onDone, 2500); return () => clearTimeout(t); }, [onDone]);
  return (
    <div style={{
      position: "fixed", bottom: 24, right: 24, zIndex: 9999,
      background: "#111827", color: "#fff", borderRadius: 12,
      padding: "12px 20px", fontSize: 13, fontWeight: 600,
      boxShadow: "0 8px 28px rgba(0,0,0,0.22)",
      display: "flex", alignItems: "center", gap: 8,
      fontFamily: "'Inter',sans-serif",
      animation: "fadeUp .2s ease",
    }}>
      <span style={{ color: "#10b981" }}>✓</span> {msg}
    </div>
  );
}

function Badge({ label }) {
  const colors = { Bestseller: ["#fef3c7","#92400e"], Hot: ["#fee2e2","#991b1b"], New: ["#d1fae5","#065f46"] };
  const [bg, tx] = colors[label] || ["#e5e7eb","#374151"];
  return (
    <span style={{ background: bg, color: tx, fontSize: 10, fontWeight: 700, borderRadius: 20, padding: "2px 8px", letterSpacing: 0.5, textTransform: "uppercase", fontFamily: "'Inter',sans-serif" }}>
      {label}
    </span>
  );
}

function ProductCard({ p, onAdd, isInCart, cartQty }) {
  const [hover, setHover] = useState(false);
  return (
    <div
      onMouseEnter={() => setHover(true)}
      onMouseLeave={() => setHover(false)}
      style={{
        background: "#fff", borderRadius: 16,
        boxShadow: hover ? "0 12px 36px rgba(0,0,0,0.12)" : "0 2px 8px rgba(0,0,0,0.06)",
        transform: hover ? "translateY(-3px)" : "none",
        transition: "all 0.2s ease",
        overflow: "hidden", display: "flex", flexDirection: "column",
        fontFamily: "'Inter',sans-serif",
      }}
    >
      {/* Image area */}
      <div style={{
        background: `linear-gradient(135deg, ${p.stock === 0 ? "#f3f4f6" : "#f0f9ff"} 0%, #e0f2fe 100%)`,
        height: 160, display: "flex", alignItems: "center", justifyContent: "center",
        fontSize: 68, position: "relative",
      }}>
        {p.emoji}
        {p.badge && (
          <div style={{ position: "absolute", top: 12, left: 12 }}>
            <Badge label={p.badge} />
          </div>
        )}
        {p.stock === 0 && (
          <div style={{
            position: "absolute", inset: 0, background: "rgba(255,255,255,0.7)",
            display: "flex", alignItems: "center", justifyContent: "center",
            fontSize: 13, fontWeight: 700, color: "#6b7280", letterSpacing: 0.5,
          }}>OUT OF STOCK</div>
        )}
        {cartQty > 0 && (
          <div style={{
            position: "absolute", top: 10, right: 10, background: "#6366f1",
            color: "#fff", borderRadius: "50%", width: 22, height: 22,
            fontSize: 11, fontWeight: 700, display: "flex", alignItems: "center", justifyContent: "center",
          }}>{cartQty}</div>
        )}
      </div>

      <div style={{ padding: "14px 16px", flex: 1, display: "flex", flexDirection: "column", gap: 6 }}>
        <p style={{ margin: 0, fontSize: 10, color: "#9ca3af", fontWeight: 600, textTransform: "uppercase", letterSpacing: 0.6 }}>
          {p.category}
        </p>
        <h3 style={{ margin: 0, fontSize: 14, fontWeight: 700, color: "#111827", lineHeight: 1.3 }}>{p.name}</h3>
        <div style={{ display: "flex", alignItems: "center", gap: 6 }}>
          <Stars r={p.rating} />
          <span style={{ fontSize: 11, color: "#9ca3af" }}>({p.reviews})</span>
        </div>
        <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginTop: "auto", paddingTop: 8 }}>
          <span style={{ fontSize: 18, fontWeight: 800, color: "#111827" }}>{fmt(p.price)}</span>
          <span style={{ fontSize: 11, color: p.stock > 0 && p.stock < 10 ? "#f59e0b" : p.stock === 0 ? "#ef4444" : "#10b981", fontWeight: 600 }}>
            {p.stock === 0 ? "Unavailable" : p.stock < 10 ? `Only ${p.stock} left` : "In stock"}
          </span>
        </div>
      </div>

      <button
        onClick={() => p.stock > 0 && onAdd(p)}
        disabled={p.stock === 0}
        style={{
          margin: "0 16px 16px", padding: "10px 0", borderRadius: 10, border: "none",
          background: p.stock === 0 ? "#e5e7eb" : isInCart ? "#6366f1" : "#111827",
          color: p.stock === 0 ? "#9ca3af" : "#fff",
          fontSize: 13, fontWeight: 700, cursor: p.stock === 0 ? "not-allowed" : "pointer",
          fontFamily: "'Inter',sans-serif", transition: "background 0.15s",
        }}
      >
        {p.stock === 0 ? "Out of Stock" : isInCart ? "✓ Add More" : "+ Add to Cart"}
      </button>
    </div>
  );
}

function CartDrawer({ cart, onClose, onQtyChange, onRemove, onCheckout }) {
  const total = cart.reduce((s, i) => s + i.price * i.qty, 0);
  const count = cart.reduce((s, i) => s + i.qty, 0);

  return (
    <>
      <div onClick={onClose} style={{ position: "fixed", inset: 0, background: "rgba(0,0,0,0.4)", zIndex: 200 }} />
      <div style={{
        position: "fixed", top: 0, right: 0, height: "100%", width: 380, maxWidth: "95vw",
        background: "#fff", zIndex: 201, display: "flex", flexDirection: "column",
        boxShadow: "-8px 0 40px rgba(0,0,0,0.14)", fontFamily: "'Inter',sans-serif",
      }}>
        <div style={{ padding: "20px 24px", borderBottom: "1px solid #f3f4f6", display: "flex", justifyContent: "space-between", alignItems: "center" }}>
          <h2 style={{ margin: 0, fontSize: 18, fontWeight: 800, color: "#111827" }}>
            Your Cart <span style={{ color: "#6366f1" }}>({count})</span>
          </h2>
          <button onClick={onClose} style={{ background: "none", border: "none", fontSize: 22, cursor: "pointer", color: "#9ca3af" }}>×</button>
        </div>

        <div style={{ flex: 1, overflowY: "auto", padding: "16px 24px", display: "flex", flexDirection: "column", gap: 14 }}>
          {cart.length === 0 ? (
            <div style={{ textAlign: "center", padding: "60px 0", color: "#9ca3af" }}>
              <p style={{ fontSize: 48, margin: 0 }}>🛒</p>
              <p style={{ fontWeight: 600, marginTop: 12 }}>Your cart is empty</p>
              <p style={{ fontSize: 13 }}>Add some items to get started.</p>
            </div>
          ) : cart.map(item => (
            <div key={item.id} style={{
              display: "flex", gap: 12, alignItems: "center",
              background: "#f9fafb", borderRadius: 12, padding: "12px 14px",
            }}>
              <span style={{ fontSize: 32 }}>{item.emoji}</span>
              <div style={{ flex: 1, minWidth: 0 }}>
                <p style={{ margin: 0, fontSize: 13, fontWeight: 700, color: "#111827", whiteSpace: "nowrap", overflow: "hidden", textOverflow: "ellipsis" }}>{item.name}</p>
                <p style={{ margin: "2px 0 0", fontSize: 13, color: "#6366f1", fontWeight: 700 }}>{fmt(item.price)}</p>
              </div>
              <div style={{ display: "flex", alignItems: "center", gap: 6 }}>
                <button onClick={() => onQtyChange(item.id, item.qty - 1)} style={qBtnStyle}>{item.qty <= 1 ? "🗑" : "−"}</button>
                <span style={{ minWidth: 20, textAlign: "center", fontSize: 14, fontWeight: 700 }}>{item.qty}</span>
                <button onClick={() => onQtyChange(item.id, item.qty + 1)} style={qBtnStyle}>+</button>
              </div>
            </div>
          ))}
        </div>

        {cart.length > 0 && (
          <div style={{ padding: "16px 24px", borderTop: "1px solid #f3f4f6" }}>
            <div style={{ display: "flex", justifyContent: "space-between", marginBottom: 6 }}>
              <span style={{ color: "#6b7280", fontSize: 13 }}>Subtotal</span>
              <span style={{ fontWeight: 700 }}>{fmt(total)}</span>
            </div>
            <div style={{ display: "flex", justifyContent: "space-between", marginBottom: 14 }}>
              <span style={{ color: "#6b7280", fontSize: 13 }}>Shipping</span>
              <span style={{ color: "#10b981", fontWeight: 600, fontSize: 13 }}>Free</span>
            </div>
            <div style={{ display: "flex", justifyContent: "space-between", marginBottom: 18, paddingTop: 12, borderTop: "1px solid #e5e7eb" }}>
              <span style={{ fontWeight: 800, fontSize: 16 }}>Total</span>
              <span style={{ fontWeight: 800, fontSize: 18, color: "#111827" }}>{fmt(total)}</span>
            </div>
            <button onClick={onCheckout} style={{
              width: "100%", padding: "13px 0", borderRadius: 12, border: "none",
              background: "linear-gradient(135deg, #6366f1 0%, #8b5cf6 100%)",
              color: "#fff", fontSize: 15, fontWeight: 800, cursor: "pointer",
              fontFamily: "'Inter',sans-serif", boxShadow: "0 4px 16px rgba(99,102,241,0.35)",
            }}>
              Checkout — {fmt(total)}
            </button>
          </div>
        )}
      </div>
    </>
  );
}

const qBtnStyle = {
  width: 28, height: 28, borderRadius: 8, border: "1.5px solid #e5e7eb",
  background: "#fff", cursor: "pointer", fontSize: 14, fontWeight: 700,
  display: "flex", alignItems: "center", justifyContent: "center",
  fontFamily: "'Inter',sans-serif",
};

function OrdersPanel() {
  const statusColors = { Delivered: ["#d1fae5","#065f46"], Shipped: ["#dbeafe","#1e40af"], Processing: ["#fef3c7","#92400e"] };
  return (
    <div style={{ fontFamily: "'Inter',sans-serif" }}>
      <h2 style={{ margin: "0 0 20px", fontSize: 20, fontWeight: 800, color: "#111827" }}>Order History</h2>
      <div style={{ display: "flex", flexDirection: "column", gap: 14 }}>
        {ORDERS.map(o => {
          const [bg, tx] = statusColors[o.status] || ["#e5e7eb","#374151"];
          return (
            <div key={o.id} style={{
              background: "#fff", borderRadius: 14, padding: "18px 20px",
              boxShadow: "0 2px 8px rgba(0,0,0,0.06)",
              display: "flex", justifyContent: "space-between", alignItems: "center", gap: 16, flexWrap: "wrap",
            }}>
              <div>
                <div style={{ display: "flex", alignItems: "center", gap: 10, marginBottom: 6 }}>
                  <span style={{ fontWeight: 800, fontSize: 14, color: "#111827" }}>{o.id}</span>
                  <span style={{ background: bg, color: tx, borderRadius: 20, padding: "2px 10px", fontSize: 11, fontWeight: 700 }}>{o.status}</span>
                </div>
                <p style={{ margin: 0, fontSize: 12, color: "#9ca3af" }}>{new Date(o.date).toLocaleDateString("en-US", { month: "short", day: "numeric", year: "numeric" })}</p>
                <p style={{ margin: "4px 0 0", fontSize: 13, color: "#6b7280" }}>{o.items.join(", ")}</p>
              </div>
              <div style={{ textAlign: "right" }}>
                <p style={{ margin: 0, fontSize: 18, fontWeight: 800, color: "#111827" }}>{fmt(o.total)}</p>
                <button style={{
                  marginTop: 6, padding: "5px 12px", borderRadius: 8,
                  border: "1.5px solid #e5e7eb", background: "#fff",
                  fontSize: 12, fontWeight: 600, cursor: "pointer", color: "#6366f1",
                  fontFamily: "'Inter',sans-serif",
                }}>Track Order</button>
              </div>
            </div>
          );
        })}
      </div>
    </div>
  );
}

function AdminPanel({ products }) {
  return (
    <div style={{ fontFamily: "'Inter',sans-serif" }}>
      <h2 style={{ margin: "0 0 20px", fontSize: 20, fontWeight: 800, color: "#111827" }}>Admin — Product Management</h2>
      <div style={{
        background: "#fff", borderRadius: 14, boxShadow: "0 2px 8px rgba(0,0,0,0.06)", overflow: "hidden",
      }}>
        <div style={{
          display: "grid", gridTemplateColumns: "2fr 1fr 1fr 1fr 1fr",
          background: "#f9fafb", padding: "10px 18px",
          fontSize: 11, fontWeight: 700, color: "#9ca3af", textTransform: "uppercase", letterSpacing: 0.5,
          gap: 8,
        }}>
          <span>Product</span><span>Category</span><span>Price</span><span>Stock</span><span>Status</span>
        </div>
        {products.map((p, i) => (
          <div key={p.id} style={{
            display: "grid", gridTemplateColumns: "2fr 1fr 1fr 1fr 1fr",
            padding: "12px 18px", gap: 8,
            borderTop: i > 0 ? "1px solid #f3f4f6" : "none",
            alignItems: "center", fontSize: 13,
          }}>
            <span style={{ fontWeight: 600, display: "flex", gap: 8, alignItems: "center" }}>
              <span>{p.emoji}</span> {p.name}
            </span>
            <span style={{ color: "#6b7280" }}>{p.category}</span>
            <span style={{ fontWeight: 700 }}>{fmt(p.price)}</span>
            <span style={{ fontWeight: 600, color: p.stock === 0 ? "#ef4444" : p.stock < 10 ? "#f59e0b" : "#111827" }}>{p.stock}</span>
            <span style={{
              display: "inline-block",
              background: p.stock === 0 ? "#fee2e2" : "#d1fae5",
              color: p.stock === 0 ? "#991b1b" : "#065f46",
              borderRadius: 20, padding: "2px 8px", fontSize: 11, fontWeight: 700,
            }}>
              {p.stock === 0 ? "Out of Stock" : "Active"}
            </span>
          </div>
        ))}
      </div>
    </div>
  );
}

function CheckoutModal({ cart, onClose, onConfirm }) {
  const [step, setStep] = useState(1); // 1=shipping, 2=payment, 3=done
  const [form, setForm] = useState({ name: "", email: "", address: "", card: "", expiry: "", cvv: "" });
  const total = cart.reduce((s, i) => s + i.price * i.qty, 0);

  const inputSt = {
    width: "100%", padding: "9px 12px", borderRadius: 8,
    border: "1.5px solid #e5e7eb", fontSize: 13,
    fontFamily: "'Inter',sans-serif", outline: "none",
    background: "#fafafa", boxSizing: "border-box",
  };

  return (
    <>
      <div onClick={onClose} style={{ position: "fixed", inset: 0, background: "rgba(0,0,0,0.5)", zIndex: 300 }} />
      <div style={{
        position: "fixed", top: "50%", left: "50%", transform: "translate(-50%,-50%)",
        background: "#fff", borderRadius: 20, width: "100%", maxWidth: 440,
        padding: 28, zIndex: 301, fontFamily: "'Inter',sans-serif",
        boxShadow: "0 28px 70px rgba(0,0,0,0.2)", maxHeight: "90vh", overflowY: "auto",
      }}>
        {step < 3 && (
          <div style={{ marginBottom: 22 }}>
            <div style={{ display: "flex", gap: 6, marginBottom: 18 }}>
              {[1, 2].map(s => (
                <div key={s} style={{ flex: 1, height: 4, borderRadius: 4, background: s <= step ? "#6366f1" : "#e5e7eb", transition: "background .3s" }} />
              ))}
            </div>
            <h2 style={{ margin: 0, fontSize: 18, fontWeight: 800, color: "#111827" }}>
              {step === 1 ? "Shipping Details" : "Payment"}
            </h2>
          </div>
        )}

        {step === 1 && (
          <div style={{ display: "flex", flexDirection: "column", gap: 12 }}>
            {[["Full Name", "name", "text"], ["Email", "email", "email"], ["Delivery Address", "address", "text"]].map(([label, key, type]) => (
              <div key={key}>
                <label style={{ fontSize: 11, fontWeight: 600, color: "#6b7280", display: "block", marginBottom: 4 }}>{label.toUpperCase()}</label>
                <input style={inputSt} type={type} value={form[key]} placeholder={label} onChange={e => setForm({ ...form, [key]: e.target.value })} />
              </div>
            ))}
            <button onClick={() => setStep(2)} style={checkoutBtnSt}>Continue to Payment →</button>
            <button onClick={onClose} style={{ ...checkoutBtnSt, background: "#f9fafb", color: "#374151", boxShadow: "none" }}>Cancel</button>
          </div>
        )}

        {step === 2 && (
          <div style={{ display: "flex", flexDirection: "column", gap: 12 }}>
            <div style={{ background: "#f9fafb", borderRadius: 12, padding: "12px 14px", marginBottom: 4 }}>
              <p style={{ margin: 0, fontSize: 12, color: "#9ca3af" }}>Order Total</p>
              <p style={{ margin: "4px 0 0", fontSize: 22, fontWeight: 800, color: "#111827" }}>{fmt(total)}</p>
            </div>
            {[["Card Number", "card", "1234 5678 9012 3456"], ["Expiry", "expiry", "MM/YY"], ["CVV", "cvv", "•••"]].map(([label, key, ph]) => (
              <div key={key}>
                <label style={{ fontSize: 11, fontWeight: 600, color: "#6b7280", display: "block", marginBottom: 4 }}>{label.toUpperCase()}</label>
                <input style={inputSt} value={form[key]} placeholder={ph} onChange={e => setForm({ ...form, [key]: e.target.value })} />
              </div>
            ))}
            <button onClick={() => { setStep(3); onConfirm(); }} style={checkoutBtnSt}>Pay {fmt(total)}</button>
            <button onClick={() => setStep(1)} style={{ ...checkoutBtnSt, background: "#f9fafb", color: "#374151", boxShadow: "none" }}>← Back</button>
          </div>
        )}

        {step === 3 && (
          <div style={{ textAlign: "center", padding: "20px 0" }}>
            <div style={{ fontSize: 64, marginBottom: 16 }}>🎉</div>
            <h2 style={{ margin: 0, fontSize: 22, fontWeight: 800, color: "#111827" }}>Order Placed!</h2>
            <p style={{ color: "#6b7280", margin: "10px 0 24px", fontSize: 14, lineHeight: 1.6 }}>
              Your order has been confirmed. You'll receive a confirmation email shortly.
            </p>
            <div style={{ background: "#f0fdf4", borderRadius: 12, padding: "12px 16px", marginBottom: 22 }}>
              <p style={{ margin: 0, fontSize: 12, color: "#15803d" }}>Order ID: <strong>ORD-{Math.floor(Math.random() * 9000 + 1000)}</strong></p>
              <p style={{ margin: "4px 0 0", fontSize: 12, color: "#15803d" }}>Estimated delivery: 3–5 business days</p>
            </div>
            <button onClick={onClose} style={checkoutBtnSt}>Back to Store</button>
          </div>
        )}
      </div>
    </>
  );
}

const checkoutBtnSt = {
  width: "100%", padding: "11px 0", borderRadius: 10, border: "none",
  background: "linear-gradient(135deg, #6366f1 0%, #8b5cf6 100%)",
  color: "#fff", fontSize: 14, fontWeight: 700, cursor: "pointer",
  fontFamily: "'Inter',sans-serif", boxShadow: "0 4px 14px rgba(99,102,241,0.3)",
};

// ─── App ──────────────────────────────────────────────────────────────────────
export default function App() {
  const [role, setRole] = useState("user"); // "user" | "admin"
  const [page, setPage] = useState("shop"); // "shop" | "orders" | "admin"
  const [cart, setCart] = useState([]);
  const [cartOpen, setCartOpen] = useState(false);
  const [checkoutOpen, setCheckoutOpen] = useState(false);
  const [category, setCategory] = useState("All");
  const [search, setSearch] = useState("");
  const [sortBy, setSortBy] = useState("default");
  const [toast, setToast] = useState(null);

  const showToast = (msg) => setToast(msg);

  const cartCount = cart.reduce((s, i) => s + i.qty, 0);

  const addToCart = (p) => {
    setCart(prev => {
      const ex = prev.find(i => i.id === p.id);
      if (ex) return prev.map(i => i.id === p.id ? { ...i, qty: i.qty + 1 } : i);
      return [...prev, { ...p, qty: 1 }];
    });
    showToast(`${p.name} added to cart`);
  };

  const changeQty = (id, qty) => {
    if (qty < 1) setCart(prev => prev.filter(i => i.id !== id));
    else setCart(prev => prev.map(i => i.id === id ? { ...i, qty } : i));
  };

  const handleCheckout = () => {
    setCartOpen(false);
    setCheckoutOpen(true);
  };

  const handleOrderConfirm = () => {
    setCart([]);
  };

  let displayed = [...PRODUCTS];
  if (category !== "All") displayed = displayed.filter(p => p.category === category);
  if (search) displayed = displayed.filter(p => p.name.toLowerCase().includes(search.toLowerCase()));
  if (sortBy === "price-asc") displayed.sort((a, b) => a.price - b.price);
  if (sortBy === "price-desc") displayed.sort((a, b) => b.price - a.price);
  if (sortBy === "rating") displayed.sort((a, b) => b.rating - a.rating);

  const navItems = [
    { id: "shop", label: "Shop", icon: "🛍️" },
    { id: "orders", label: "My Orders", icon: "📦" },
    ...(role === "admin" ? [{ id: "admin", label: "Admin", icon: "⚙️" }] : []),
  ];

  return (
    <div style={{ minHeight: "100vh", background: "#f8fafc", fontFamily: "'Inter',sans-serif" }}>
      <style>{`@import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700;800&display=swap'); @keyframes fadeUp { from { opacity:0; transform:translateY(8px);} to {opacity:1;transform:translateY(0);}}`}</style>

      {/* Header */}
      <header style={{
        background: "#fff", borderBottom: "1px solid #f3f4f6",
        position: "sticky", top: 0, zIndex: 100,
        boxShadow: "0 1px 4px rgba(0,0,0,0.06)",
      }}>
        <div style={{
          maxWidth: 1200, margin: "0 auto", padding: "0 24px",
          display: "flex", alignItems: "center", gap: 16, height: 60,
        }}>
          <div style={{ fontWeight: 800, fontSize: 20, color: "#111827", letterSpacing: -0.5, display: "flex", alignItems: "center", gap: 8 }}>
            <span style={{
              background: "linear-gradient(135deg,#6366f1,#8b5cf6)",
              borderRadius: 8, width: 30, height: 30,
              display: "inline-flex", alignItems: "center", justifyContent: "center",
              fontSize: 15, color: "#fff",
            }}>🛒</span>
            ShopNow
          </div>

          <nav style={{ display: "flex", gap: 4, flex: 1 }}>
            {navItems.map(n => (
              <button key={n.id} onClick={() => setPage(n.id)} style={{
                padding: "6px 12px", borderRadius: 8, border: "none",
                background: page === n.id ? "#f0f0fe" : "transparent",
                color: page === n.id ? "#6366f1" : "#6b7280",
                fontWeight: page === n.id ? 700 : 500, fontSize: 13,
                cursor: "pointer", fontFamily: "'Inter',sans-serif",
                display: "flex", alignItems: "center", gap: 5,
              }}>
                {n.icon} {n.label}
              </button>
            ))}
          </nav>

          {/* Role toggle */}
          <div style={{ display: "flex", gap: 4, background: "#f3f4f6", borderRadius: 8, padding: 3 }}>
            {["user","admin"].map(r => (
              <button key={r} onClick={() => { setRole(r); if (r === "user" && page === "admin") setPage("shop"); }} style={{
                padding: "4px 12px", borderRadius: 6, border: "none",
                background: role === r ? "#fff" : "transparent",
                color: role === r ? "#111827" : "#9ca3af",
                fontWeight: 700, fontSize: 11, cursor: "pointer",
                fontFamily: "'Inter',sans-serif", textTransform: "capitalize",
                boxShadow: role === r ? "0 1px 4px rgba(0,0,0,0.08)" : "none",
              }}>
                {r === "admin" ? "⚙️ " : "👤 "}{r}
              </button>
            ))}
          </div>

          {/* Cart */}
          <button onClick={() => setCartOpen(true)} style={{
            position: "relative", background: "#111827", color: "#fff",
            border: "none", borderRadius: 10, padding: "8px 14px",
            fontSize: 13, fontWeight: 700, cursor: "pointer",
            display: "flex", alignItems: "center", gap: 6,
            fontFamily: "'Inter',sans-serif",
          }}>
            🛒 Cart
            {cartCount > 0 && (
              <span style={{
                position: "absolute", top: -6, right: -6,
                background: "#6366f1", color: "#fff",
                borderRadius: "50%", width: 20, height: 20,
                fontSize: 11, fontWeight: 800,
                display: "flex", alignItems: "center", justifyContent: "center",
              }}>{cartCount}</span>
            )}
          </button>
        </div>
      </header>

      {/* Main */}
      <main style={{ maxWidth: 1200, margin: "0 auto", padding: "28px 24px 60px" }}>
        {page === "shop" && (
          <>
            {/* Filters */}
            <div style={{ display: "flex", gap: 12, marginBottom: 20, flexWrap: "wrap", alignItems: "center" }}>
              <input
                value={search} onChange={e => setSearch(e.target.value)}
                placeholder="🔍  Search products…"
                style={{
                  flex: 1, minWidth: 200, padding: "9px 14px", borderRadius: 10,
                  border: "1.5px solid #e5e7eb", fontSize: 13,
                  fontFamily: "'Inter',sans-serif", outline: "none", background: "#fff",
                }}
              />
              <select value={sortBy} onChange={e => setSortBy(e.target.value)} style={{
                padding: "9px 12px", borderRadius: 10, border: "1.5px solid #e5e7eb",
                fontSize: 13, fontFamily: "'Inter',sans-serif", background: "#fff", cursor: "pointer",
              }}>
                <option value="default">Sort: Default</option>
                <option value="price-asc">Price: Low → High</option>
                <option value="price-desc">Price: High → Low</option>
                <option value="rating">Top Rated</option>
              </select>
            </div>

            {/* Category pills */}
            <div style={{ display: "flex", gap: 8, marginBottom: 24, overflowX: "auto", paddingBottom: 4 }}>
              {CATEGORIES.map(c => (
                <button key={c} onClick={() => setCategory(c)} style={{
                  padding: "6px 16px", borderRadius: 20, border: "1.5px solid",
                  borderColor: category === c ? "#6366f1" : "#e5e7eb",
                  background: category === c ? "#f0f0fe" : "#fff",
                  color: category === c ? "#6366f1" : "#6b7280",
                  fontWeight: 600, fontSize: 12, cursor: "pointer", whiteSpace: "nowrap",
                  fontFamily: "'Inter',sans-serif",
                }}>
                  {c}
                </button>
              ))}
            </div>

            {/* Grid */}
            <div style={{ display: "grid", gridTemplateColumns: "repeat(auto-fill, minmax(220px, 1fr))", gap: 18 }}>
              {displayed.map(p => (
                <ProductCard
                  key={p.id} p={p}
                  onAdd={addToCart}
                  isInCart={cart.some(i => i.id === p.id)}
                  cartQty={cart.find(i => i.id === p.id)?.qty || 0}
                />
              ))}
            </div>
            {displayed.length === 0 && (
              <div style={{ textAlign: "center", padding: "60px 0", color: "#9ca3af" }}>
                <p style={{ fontSize: 48, margin: 0 }}>🔍</p>
                <p style={{ fontWeight: 600, marginTop: 12 }}>No products found</p>
              </div>
            )}
          </>
        )}

        {page === "orders" && <OrdersPanel />}
        {page === "admin" && role === "admin" && <AdminPanel products={PRODUCTS} />}
        {page === "admin" && role !== "admin" && (
          <div style={{ textAlign: "center", padding: "80px 0" }}>
            <p style={{ fontSize: 48 }}>🔒</p>
            <p style={{ fontWeight: 700, fontSize: 18, color: "#111827" }}>Admin access only</p>
            <p style={{ color: "#9ca3af" }}>Switch to Admin role to view this page.</p>
          </div>
        )}
      </main>

      {/* Cart drawer */}
      {cartOpen && (
        <CartDrawer
          cart={cart}
          onClose={() => setCartOpen(false)}
          onQtyChange={changeQty}
          onRemove={id => setCart(prev => prev.filter(i => i.id !== id))}
          onCheckout={handleCheckout}
        />
      )}

      {/* Checkout */}
      {checkoutOpen && (
        <CheckoutModal
          cart={cart}
          onClose={() => setCheckoutOpen(false)}
          onConfirm={handleOrderConfirm}
        />
      )}

      {/* Toast */}
      {toast && <Toast msg={toast} onDone={() => setToast(null)} />}
    </div>
  );
}
