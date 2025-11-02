/* Online Shop - Single-file React component Drop this into a React + Tailwind project (Vite or CRA) as App.jsx. Requirements:

React 18+

Tailwind CSS configured in the project

Optional: lucide-react for icons (install if you want) or remove icon imports


What this file includes:

Responsive product grid

Product quick view modal

Cart sidebar with add/remove/update quantity

Simple checkout mock (no payment)

LocalStorage persistence for cart

Example product data (replace with your own or connect to an API)


How to use:

1. Create a new Vite + React project or CRA and configure Tailwind.


2. Replace App.jsx with this file (or import the component).


3. Start the dev server: npm run dev or npm start.



Customize:

Replace PRODUCTS array with your product API or CMS

Hook up real checkout by sending cart to backend

Add authentication and order history as needed */


import React, { useEffect, useState } from "react";

// Simple icon replacements if you don't want external icon libs function IconCart({ className = "w-5 h-5" }) { return ( <svg className={className} fill="none" viewBox="0 0 24 24" stroke="currentColor"> <path strokeWidth="1.5" strokeLinecap="round" strokeLinejoin="round" d="M3 3h2l.4 2M7 13h10l4-8H5.4" /> <circle cx="10" cy="20" r="1" /> <circle cx="18" cy="20" r="1" /> </svg> ); } function IconClose({ className = "w-5 h-5" }) { return ( <svg className={className} viewBox="0 0 24 24" fill="none" stroke="currentColor"> <path strokeWidth="1.5" strokeLinecap="round" strokeLinejoin="round" d="M6 18L18 6M6 6l12 12" /> </svg> ); }

const PRODUCTS = [ { id: "p1", title: "Classic Leather Jacket", price: 129.99, img: "https://images.unsplash.com/photo-1520975698519-9b0baf2a2a12?w=800&q=60", desc: "Premium faux-leather jacket with soft lining and modern cut. Available in S-XL.", stock: 12, }, { id: "p2", title: "Minimalist Watch", price: 79.99, img: "https://images.unsplash.com/photo-1519744792095-2f2205e87b6f?w=800&q=60", desc: "Sleek, water-resistant watch with stainless steel case and leather strap.", stock: 25, }, { id: "p3", title: "Everyday Backpack", price: 59.0, img: "https://images.unsplash.com/photo-1505740420928-5e560c06d30e?w=800&q=60", desc: "Durable polyester backpack with laptop sleeve and multiple pockets.", stock: 30, }, { id: "p4", title: "Wireless Headphones", price: 99.5, img: "https://images.unsplash.com/photo-1518444024560-1f8b5a8a18c6?w=800&q=60", desc: "Noise-cancelling over-ear headphones with 20h battery life.", stock: 18, }, { id: "p5", title: "Sneaker Runner", price: 69.99, img: "https://images.unsplash.com/photo-1520975698519-9b0baf2a2a12?w=800&q=60&crop=faces&fit=crop", desc: "Lightweight sneakers built for comfort and everyday wear.", stock: 40, }, ];

export default function App() { const [products] = useState(PRODUCTS); const [cart, setCart] = useState(() => { try { const raw = localStorage.getItem("shop_cart_v1"); return raw ? JSON.parse(raw) : {}; } catch (e) { return {}; } }); const [isCartOpen, setCartOpen] = useState(false); const [query, setQuery] = useState(""); const [selected, setSelected] = useState(null); const [successMessage, setSuccessMessage] = useState("");

useEffect(() => { localStorage.setItem("shop_cart_v1", JSON.stringify(cart)); }, [cart]);

function addToCart(productId, qty = 1) { setCart((c) => { const prev = c[productId] ? c[productId].qty : 0; const updated = { ...c, [productId]: { id: productId, qty: prev + qty } }; return updated; }); setCartOpen(true); }

function updateQty(productId, qty) { setCart((c) => { if (!c[productId]) return c; if (qty <= 0) { const copy = { ...c }; delete copy[productId]; return copy; } return { ...c, [productId]: { ...c[productId], qty } }; }); }

function clearCart() { setCart({}); }

function totalItems() { return Object.values(cart).reduce((s, i) => s + i.qty, 0); }

function cartLines() { return Object.values(cart).map((line) => { const prod = products.find((p) => p.id === line.id); return { ...line, title: prod.title, price: prod.price, img: prod.img, }; }); }

function subtotal() { return cartLines().reduce((s, l) => s + l.qty * l.price, 0); }

function fakeCheckout() { // In a real app, you'd send the cart to a backend now. setSuccessMessage("Order placed — thank you! (This is a demo checkout.)"); clearCart(); setTimeout(() => setSuccessMessage(""), 4000); setCartOpen(false); }

const filtered = products.filter((p) => p.title.toLowerCase().includes(query.toLowerCase()));

return ( <div className="min-h-screen bg-gray-50 text-gray-900"> {/* Header */} <header className="bg-white shadow-sm"> <div className="max-w-6xl mx-auto px-4 py-4 flex items-center justify-between"> <div className="flex items-center gap-4"> <div className="text-2xl font-bold">Aurora Shop</div> <nav className="hidden md:flex gap-4 text-sm text-gray-600"> <a className="hover:underline" href="#home">Home</a> <a className="hover:underline" href="#products">Products</a> <a className="hover:underline" href="#contact">Contact</a> </nav> </div>

<div className="flex items-center gap-3">
        <div className="hidden sm:block">
          <input
            value={query}
            onChange={(e) => setQuery(e.target.value)}
            placeholder="Search products..."
            className="border rounded-lg px-3 py-2 w-64 focus:outline-none"
          />
        </div>

        <button
          onClick={() => setCartOpen(true)}
          className="relative rounded-md px-3 py-2 border flex items-center gap-2 hover:shadow">
          <IconCart />
          <span className="text-sm">Cart</span>
          {totalItems() > 0 && (
            <span className="absolute -top-2 -right-2 bg-red-500 text-white text-xs rounded-full px-2">{totalItems()}</span>
          )}
        </button>
      </div>
    </div>
  </header>

  {/* Hero */}
  <section id="home" className="max-w-6xl mx-auto px-4 py-12">
    <div className="grid grid-cols-1 md:grid-cols-2 gap-8 items-center">
      <div>
        <h1 className="text-4xl font-extrabold mb-4">Shop modern goods — quality, curated.</h1>
        <p className="text-gray-600 mb-6">Fast shipping · Secure checkout · 30-day returns</p>
        <div className="flex gap-3">
          <a href="#products" className="px-5 py-3 bg-indigo-600 text-white rounded-md shadow hover:opacity-95">Browse Products</a>
          <button onClick={() => { setQuery(""); window.scrollTo({ top: 800, behavior: 'smooth' }); }} className="px-5 py-3 border rounded-md">View All</button>
        </div>
      </div>

      <div className="rounded-lg overflow-hidden shadow-md">
        <img src="https://images.unsplash.com/photo-1523275335684-37898b6baf30?w=1200&q=60" alt="shop hero" className="w-full h-64 object-cover" />
      </div>
    </div>
  </section>

  {/* Products */}
  <section id="products" className="max-w-6xl mx-auto px-4 py-8">
    <div className="flex items-center justify-between mb-6">
      <h2 className="text-2xl font-semibold">Products</h2>
      <div className="text-sm text-gray-600">Showing {filtered.length} of {products.length}</div>
    </div>

    {filtered.length === 0 ? (
      <div className="text-center py-20 text-gray-500">No products found</div>
    ) : (
      <div className="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-6">
        {filtered.map((p) => (
          <div key={p.id} className="bg-white rounded-lg shadow-sm overflow-hidden">
            <div className="h-48 bg-gray-100 overflow-hidden flex items-center justify-center">
              <img src={p.img} alt={p.title} className="w-full h-full object-cover" />
            </div>
            <div className="p-4">
              <h3 className="font-medium">{p.title}</h3>
              <p className="text-sm text-gray-500 mt-1">{p.desc}</p>
              <div className="mt-3 flex items-center justify-between">
                <div className="text-lg font-semibold">${p.price.toFixed(2)}</div>
                <div className="flex gap-2">
                  <button
                    onClick={() => { setSelected(p); }}
                    className="px-3 py-1 border rounded-md text-sm">Quick View</button>
                  <button
                    onClick={() => addToCart(p.id, 1)}
                    className="px-3 py-1 bg-indigo-600 text-white rounded-md text-sm">Add</button>
                </div>
              </div>
            </div>
          </div>
        ))}
      </div>
    )}
  </section>

  {/* Contact / Footer */}
  <footer id="contact" className="bg-white border-t mt-10">
    <div className="max-w-6xl mx-auto px-4 py-10 grid grid-cols-1 md:grid-cols-3 gap-6">
      <div>
        <h4 className="font-bold">Aurora Shop</h4>
        <p className="text-sm text-gray-600 mt-2">Quality goods, curated with care. Contact us at hello@aurorashop.example</p>
      </div>
      <div>
        <h5 className="font-semibold">Help</h5>
        <ul className="text-sm text-gray-600 mt-2 space-y-1">
          <li>Shipping & returns</li>
          <li>FAQ</li>
          <li>Support center</li>
        </ul>
      </div>
      <div>
        <h5 className="font-semibold">Newsletter</h5>
        <p className="text-sm text-gray-600 mt-2 mb-3">Get updates and offers</p>
        <div className="flex gap-2">
          <input className="px-3 py-2 border rounded-md" placeholder="Email address" />
          <button className="px-3 py-2 bg-indigo-600 text-white rounded-md">Subscribe</button>
        </div>
      </div>
    </div>
    <div className="text-center text-sm text-gray-500 py-4">© {new Date().getFullYear()} Aurora Shop. All rights reserved.</div>
  </footer>

  {/* Product Modal */}
  {selected && (
    <div className="fixed inset-0 bg-black/40 flex items-center justify-center z-40">
      <div className="bg-white rounded-lg max-w-3xl w-full mx-4 overflow-hidden shadow-lg">
        <div className="flex justify-between items-start p-4">
          <div>
            <h3 className="text-xl font-semibold">{selected.title}</h3>
            <div className="text-sm text-gray-500">${selected.price.toFixed(2)}</div>
          </div>
          <button onClick={() => setSelected(null)} className="p-2">
            <IconClose />
          </button>
        </div>
        <div className="grid grid-cols-1 md:grid-cols-2 gap-4 p-4">
          <div className="h-80 overflow-hidden">
            <img src={selected.img} alt={selected.title} className="w-full h-full object-cover" />
          </div>
          <div>
            <p className="text-gray-700 mb-4">{selected.desc}</p>
            <div className="mb-4">Stock: <strong>{selected.stock}</strong></div>
            <div className="flex gap-3">
              <button onClick={() => { addToCart(selected.id, 1); setSelected(null); }} className="px-4 py-2 bg-indigo-600 text-white rounded-md">Add to cart</button>
              <button onClick={() => setSelected(null)} className="px-4 py-2 border rounded-md">Close</button>
            </div>
          </div>
        </div>
      </div>
    </div>
  )}

  {/* Cart Sidebar */}
  <div className={`fixed top-0 right-0 h-full w-full md:w-96 bg-white shadow-xl z-50 transform transition-transform ${isCartOpen ? "translate-x-0" : "translate-x-full"}`}>
    <div className="p-4 border-b flex items-center justify-between">
      <div className="flex items-center gap-3">
        <IconCart />
        <div className="font-semibold">Your Cart</div>
      </div>
      <div className="flex items-center gap-2">
        <div className="text-sm text-gray-500">{totalItems()} items</div>
        <button onClick={() => setCartOpen(false)} className="p-2"><IconClose /></button>
      </div>
    </div>

    <div className="p-4 h-[calc(100%-160px)] overflow-auto">
      {cartLines().length === 0 ? (
        <div className="text-center text-gray-500 py-20">Your cart is empty</div>
      ) : (
        <div className="space-y-4">
          {cartLines().map((line) => (
            <div key={line.id} className="flex gap-3 items-center">
              <img src={line.img} alt={line.title} className="w-16 h-16 object-cover rounded-md" />
              <div className="flex-1">
                <div className="font-medium">{line.title}</div>
                <div className="text-sm text-gray-500">${line.price.toFixed(2)} each</div>
                <div className="mt-2 flex items-center gap-2">
                  <button onClick={() => updateQty(line.id, line.qty - 1)} className="px-2 py-1 border rounded">-</button>
                  <div className="px-3">{line.qty}</div>
                  <button onClick={() => updateQty(line.id, line.qty + 1)} className="px-2 py-1 border rounded">+</button>
                </div>
              </div>
            </div>
          ))}
        </div>
      )}
    </div>

    <div className="p-4 border-t">
      <div className="flex items-center justify-between mb-3">
        <div className="text-sm text-gray-600">Subtotal</div>
        <div className="font-semibold">${subtotal().toFixed(2)}</div>
      </div>
      <div className="flex gap-2">
        <button onClick={() => { fakeCheckout(); }} className="flex-1 px-4 py-2 bg-green-600 text-white rounded-md">Checkout</button>
        <button onClick={() => clearCart()} className="px-4 py-2 border rounded-md">Clear</button>
      </div>
    </div>
  </div>

  {/* Success toast */}
  {successMessage && (
    <div className="fixed left-1/2 -translate-x-1/2 bottom-8 bg-green-600 text-white px-5 py-3 rounded-md shadow z-60">{successMessage}</div>
  )}
</div>

); }
