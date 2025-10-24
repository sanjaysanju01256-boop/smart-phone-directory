import { initializeApp } from "https://www.gstatic.com/firebasejs/12.4.0/firebase-app.js";
import { getDatabase, ref, push, set, onValue, remove } from "https://www.gstatic.com/firebasejs/12.4.0/firebase-database.js";

// ===== Firebase Config =====
const firebaseConfig = {
    apiKey: "AIzaSyAJBIkiReoNsmyksaYoRNLgIMaT-9VBFaU",
    authDomain: "smartphonedirectory-c7e53.firebaseapp.com",
    databaseURL: "https://smartphonedirectory-c7e53-default-rtdb.firebaseio.com",
    projectId: "smartphonedirectory-c7e53",
    storageBucket: "smartphonedirectory-c7e53.appspot.com",
    messagingSenderId: "1076796478965",
    appId: "1:1076796478965:web:8bb6aa536c5e9a7fb13cbc"
};

// ===== Initialize Firebase =====
const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

// ===== DOM Elements =====
const contactForm = document.getElementById('contactForm');
const contactName = document.getElementById('contactName');
const contactPhone = document.getElementById('contactPhone');
const contactsList = document.getElementById('contactsList');
const searchInput = document.getElementById('searchInput');
const clearSearch = document.getElementById('clearSearch');
const noContacts = document.getElementById('noContacts');

const EXPIRY_DAYS = 20;
let contacts = [];

// ===== Display Contacts =====
function displayContacts(list) {
    contactsList.innerHTML = '';
    if (list.length === 0) {
        noContacts.style.display = 'block';
        return;
    } else {
        noContacts.style.display = 'none';
    }

    list.forEach(c => {
        const div = document.createElement('div');
        div.className = 'contact-item';
        div.innerHTML = `
            <span>${c.name}</span>
            <span>${c.phone}</span>
            <button class="delete-btn" data-key="${c.key}">Delete</button>
        `;
        contactsList.appendChild(div);
    });

    document.querySelectorAll('.delete-btn').forEach(btn => {
        btn.addEventListener('click', e => {
            const key = e.target.getAttribute('data-key');
            remove(ref(db, 'contacts/' + key));
        });
    });
}

// ===== Search Contacts =====
function searchContacts() {
    const query = searchInput.value.toLowerCase();
    const filtered = contacts.filter(c => 
        c.name.toLowerCase().includes(query) ||
        c.phone.includes(query)
    );
    displayContacts(filtered);
}

// ===== Add Contact =====
contactForm.addEventListener('submit', e => {
    e.preventDefault();
    const name = contactName.value.trim();
    const phone = contactPhone.value.trim();
    if (!name || !phone) return;

    const newContactRef = push(ref(db, 'contacts'));
    const addedDate = new Date().toISOString();
    set(newContactRef, { name, phone, added: addedDate });

    contactForm.reset();
});

// ===== Firebase Listener =====
onValue(ref(db, 'contacts'), snapshot => {
    contacts = [];
    snapshot.forEach(child => {
        const data = child.val();
        const key = child.key;
        const added = new Date(data.added);
        const now = new Date();
        const diff = (now - added) / (1000*60*60*24);

        if (diff <= EXPIRY_DAYS) {
            contacts.push({ ...data, key });
        } else {
            // auto-delete expired contacts
            remove(ref(db, 'contacts/' + key));
        }
    });
    searchContacts();
});

// ===== Search & Clear Events =====
searchInput.addEventListener('input', searchContacts);
clearSearch.addEventListener('click', () => {
    searchInput.value = '';
    displayContacts(contacts);
});

document.addEventListener('keydown', e => {
    if (e.ctrlKey && e.key.toLowerCase() === 'k') {
        e.preventDefault();
        searchInput.focus();
    } else if (e.key === 'Escape') {
        searchInput.value = '';
        displayContacts(contacts);
    }
});
