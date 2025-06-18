<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Event Attendance Tracker</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
        }
        .delete-btn {
            transition: background-color 0.2s, color 0.2s;
        }
    </style>
</head>
<body class="bg-gray-100 text-gray-800">

    <div id="app" class="container mx-auto p-4 sm:p-6 md:p-8">
        <!-- Modal for confirmations -->
        <div id="confirmation-modal" class="hidden fixed inset-0 bg-gray-600 bg-opacity-50 overflow-y-auto h-full w-full z-50">
            <div class="relative top-20 mx-auto p-5 border w-96 shadow-lg rounded-md bg-white">
                <div class="mt-3 text-center">
                    <h3 class="text-lg leading-6 font-medium text-gray-900" id="modal-title">Are you sure?</h3>
                    <div class="mt-2 px-7 py-3">
                        <p class="text-sm text-gray-500" id="modal-body">
                            This action cannot be undone.
                        </p>
                    </div>
                    <div class="items-center px-4 py-3">
                        <button id="modal-confirm-btn" class="px-4 py-2 bg-red-500 text-white text-base font-medium rounded-md w-auto shadow-sm hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-red-300">
                            Confirm
                        </button>
                        <button id="modal-cancel-btn" class="px-4 py-2 bg-gray-200 text-gray-800 text-base font-medium rounded-md w-auto ml-2 shadow-sm hover:bg-gray-300 focus:outline-none focus:ring-2 focus:ring-gray-300">
                            Cancel
                        </button>
                    </div>
                </div>
            </div>
        </div>

        <!-- Loading Spinner -->
        <div id="loading" class="fixed inset-0 bg-white bg-opacity-75 flex items-center justify-center z-50">
            <div class="animate-spin rounded-full h-32 w-32 border-t-2 border-b-2 border-blue-500"></div>
        </div>

        <!-- Main Content -->
        <div id="main-content" class="hidden">
            <!-- Admin Login Section -->
            <div id="admin-login-section" class="max-w-md mx-auto bg-white p-8 rounded-lg shadow-lg">
                <h1 class="text-3xl font-bold mb-6 text-center text-gray-900">Admin Login</h1>
                <p id="admin-setup-info" class="text-center text-sm text-gray-600 mb-4 hidden">No admin account found. Enter your email and a new password (min. 6 characters) to set up your account.</p>
                <div class="mb-4">
                    <label for="admin-email" class="block text-sm font-medium text-gray-700 mb-2">Email</label>
                    <input type="email" id="admin-email" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-blue-500 focus:border-blue-500" placeholder="Enter admin email">
                </div>
                <div class="mb-4">
                    <label for="admin-password" class="block text-sm font-medium text-gray-700 mb-2">Password</label>
                    <input type="password" id="admin-password" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-blue-500 focus:border-blue-500" placeholder="Enter admin password">
                </div>
                <div class="flex justify-end mb-4">
                    <a href="#" id="forgot-password-link" class="text-sm text-blue-600 hover:underline">Forgot Password?</a>
                </div>
                <button id="admin-login-btn" class="w-full bg-blue-600 text-white py-2 px-4 rounded-lg hover:bg-blue-700 transition duration-300">Login</button>
                 <button id="show-user-view-btn" class="w-full bg-gray-200 text-gray-800 py-2 px-4 rounded-lg hover:bg-gray-300 transition duration-300 mt-4">Switch to User View</button>
            </div>
            
             <!-- Forgot Password Section -->
            <div id="forgot-password-section" class="hidden max-w-md mx-auto bg-white p-8 rounded-lg shadow-lg">
                <h1 class="text-3xl font-bold mb-6 text-center text-gray-900">Retrieve Password</h1>
                 <div class="mb-4">
                    <label for="forgot-password-email" class="block text-sm font-medium text-gray-700 mb-2">Enter your Admin Email</label>
                    <input type="email" id="forgot-password-email" class="w-full px-4 py-2 border border-gray-300 rounded-lg" placeholder="admin@example.com">
                </div>
                <button id="retrieve-password-btn" class="w-full bg-teal-500 text-white py-2 px-4 rounded-lg hover:bg-teal-600">Retrieve Password</button>
                 <div id="retrieved-password-container" class="hidden mt-4 p-4 bg-yellow-100 border-l-4 border-yellow-500 text-yellow-700">
                    <p class="font-bold">Security Notice</p>
                    <p>For your convenience, your password is shown below. In a real application, this would be sent to your email.</p>
                    <p class="mt-2">Your password is: <strong id="retrieved-password" class="text-black"></strong></p>
                </div>
                <p id="retrieve-error" class="text-red-500 text-sm mt-2"></p>
                <a href="#" id="back-to-login-link" class="block text-center mt-4 text-sm text-blue-600 hover:underline">Back to Login</a>
            </div>

            <!-- Admin View -->
            <div id="admin-view" class="hidden">
                <div class="flex justify-between items-center mb-8 flex-wrap gap-4">
                    <h1 class="text-4xl font-bold text-gray-900">Admin Dashboard</h1>
                    <div class="flex items-center space-x-4">
                        <span id="admin-user-id" class="text-sm text-gray-500"></span>
                        <button id="admin-logout-btn" class="bg-red-500 text-white py-2 px-4 rounded-lg hover:bg-red-600 transition">Logout</button>
                    </div>
                </div>
                
                <div class="grid md:grid-cols-2 gap-8 mb-8">
                    <!-- Change Password -->
                    <div class="bg-white p-6 rounded-lg shadow-md">
                        <h2 class="text-2xl font-semibold mb-4">Change Admin Password</h2>
                        <div class="flex space-x-4">
                            <input type="password" id="new-admin-password" class="flex-grow px-4 py-2 border rounded-lg" placeholder="Enter new password">
                            <button id="set-password-btn" class="bg-green-500 text-white py-2 px-4 rounded-lg hover:bg-green-600">Set</button>
                        </div>
                        <p id="password-status" class="text-sm mt-2 text-green-600"></p>
                    </div>

                    <!-- Event Creation -->
                    <div class="bg-white p-6 rounded-lg shadow-md">
                        <h2 class="text-2xl font-semibold mb-4">Create New Event</h2>
                        <div class="flex flex-col sm:flex-row sm:space-x-4 space-y-2 sm:space-y-0">
                            <input type="text" id="event-name" class="flex-grow px-4 py-2 border rounded-lg" placeholder="Event Name">
                            <input type="date" id="event-date" class="px-4 py-2 border rounded-lg">
                            <button id="add-event-btn" class="bg-blue-600 text-white py-2 px-4 rounded-lg hover:bg-blue-700">Add Event</button>
                        </div>
                    </div>
                 </div>

                <!-- Manual Check-in -->
                <div class="bg-white p-6 rounded-lg shadow-md mb-8">
                    <h2 class="text-2xl font-semibold mb-4">Manual Check-in by Code</h2>
                     <div class="mb-4">
                        <label for="checkin-event-select" class="block text-sm font-medium text-gray-700 mb-1">Select Event for Check-in</label>
                        <select id="checkin-event-select" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-blue-500 focus:border-blue-500"></select>
                    </div>
                    <div class="flex space-x-4">
                        <input type="text" id="manual-checkin-code" class="flex-grow px-4 py-2 border rounded-lg uppercase" placeholder="Enter User Code">
                        <button id="manual-checkin-btn" class="bg-teal-500 text-white py-2 px-4 rounded-lg hover:bg-teal-600">Check-in</button>
                    </div>
                    <p id="checkin-status" class="text-sm mt-2"></p>
                </div>

                <!-- Event Lists -->
                <h2 class="text-3xl font-bold mb-6">Manage Events</h2>
                <div id="event-lists-container" class="space-y-8">
                </div>
            </div>
            
            <!-- User View -->
            <div id="user-view" class="hidden">
                 <div class="flex justify-between items-center mb-6 flex-wrap">
                    <h1 class="text-3xl font-bold text-gray-900">User Registration</h1>
                    <button id="show-admin-view-btn" class="bg-gray-200 text-gray-800 py-2 px-4 rounded-lg hover:bg-gray-300 transition">Switch to Admin View</button>
                </div>
                <!-- User Sign up Form -->
                <div class="max-w-lg mx-auto bg-white p-8 rounded-lg shadow-lg">
                    <div id="user-signup-form">
                        <div class="mb-4">
                            <label for="user-name" class="block text-sm font-medium text-gray-700 mb-2">Full Name</label>
                            <input type="text" id="user-name" class="w-full px-4 py-2 border border-gray-300 rounded-lg" placeholder="e.g., Jane Doe">
                        </div>
                         <div class="mb-4">
                            <label for="user-email" class="block text-sm font-medium text-gray-700 mb-2">Email Address</label>
                            <input type="email" id="user-email" class="w-full px-4 py-2 border border-gray-300 rounded-lg" placeholder="e.g., jane.doe@example.com">
                        </div>
                        <div class="mb-4">
                            <label for="user-phone" class="block text-sm font-medium text-gray-700 mb-2">Phone Number</label>
                            <input type="tel" id="user-phone" class="w-full px-4 py-2 border border-gray-300 rounded-lg" placeholder="e.g., 07123456789">
                        </div>
                        <div class="mb-4">
                            <label for="user-postcode" class="block text-sm font-medium text-gray-700 mb-2">Postcode</label>
                            <input type="text" id="user-postcode" class="w-full px-4 py-2 border border-gray-300 rounded-lg" placeholder="e.g., SW1A 1AA">
                        </div>
                        <button id="signup-btn" class="w-full bg-indigo-600 text-white py-2 px-4 rounded-lg hover:bg-indigo-700 transition duration-300">Register</button>
                    </div>
                    
                    <div id="signup-success" class="hidden mt-6 p-4 bg-green-100 text-green-800 rounded-lg">
                        <p class="font-semibold">Registration Successful!</p>
                        <p>Thank you for registering.</p>
                        <p>Your unique code is: <span id="user-code" class="font-bold text-lg"></span></p>
                        <p class="text-sm mt-2">Please keep this code safe. You'll need it for event check-ins.</p>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, doc, getDoc, setDoc, onSnapshot, collection, addDoc, query, getDocs, updateDoc, where, deleteDoc, deleteField } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";
        import { getAnalytics } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-analytics.js";

        const firebaseConfig = JSON.parse(typeof __firebase_config !== 'undefined' ? __firebase_config : '{}');
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-event-tracker';

        let app, auth, db, analytics;
        try {
            app = initializeApp(firebaseConfig);
            auth = getAuth(app);
            db = getFirestore(app);
            analytics = getAnalytics(app);
        } catch (e) {
            console.error("Firebase initialization failed:", e);
            document.getElementById('loading').innerText = "Error: Could not connect to the database.";
        }

        const loadingEl = document.getElementById('loading');
        const mainContentEl = document.getElementById('main-content');
        const adminLoginSection = document.getElementById('admin-login-section');
        const forgotPasswordSection = document.getElementById('forgot-password-section');
        const adminSetupInfo = document.getElementById('admin-setup-info');
        const adminView = document.getElementById('admin-view');
        const userView = document.getElementById('user-view');
        const adminEmailInput = document.getElementById('admin-email');
        const adminPasswordInput = document.getElementById('admin-password');
        const adminLoginBtn = document.getElementById('admin-login-btn');
        const forgotPasswordLink = document.getElementById('forgot-password-link');
        const backToLoginLink = document.getElementById('back-to-login-link');
        const forgotPasswordEmailInput = document.getElementById('forgot-password-email');
        const retrievePasswordBtn = document.getElementById('retrieve-password-btn');
        const retrievedPasswordContainer = document.getElementById('retrieved-password-container');
        const retrievedPasswordEl = document.getElementById('retrieved-password');
        const retrieveErrorEl = document.getElementById('retrieve-error');
        const adminLogoutBtn = document.getElementById('admin-logout-btn');
        const adminUserIdEl = document.getElementById('admin-user-id');
        const showUserViewBtn = document.getElementById('show-user-view-btn');
        const showAdminViewBtn = document.getElementById('show-admin-view-btn');
        const newAdminPasswordInput = document.getElementById('new-admin-password');
        const setPasswordBtn = document.getElementById('set-password-btn');
        const passwordStatusEl = document.getElementById('password-status');
        const addEventBtn = document.getElementById('add-event-btn');
        const eventNameInput = document.getElementById('event-name');
        const eventDateInput = document.getElementById('event-date');
        const eventListsContainer = document.getElementById('event-lists-container');
        const checkinEventSelect = document.getElementById('checkin-event-select');
        const manualCheckinCodeInput = document.getElementById('manual-checkin-code');
        const manualCheckinBtn = document.getElementById('manual-checkin-btn');
        const checkinStatusEl = document.getElementById('checkin-status');
        const userNameInput = document.getElementById('user-name');
        const userEmailInput = document.getElementById('user-email');
        const userPhoneInput = document.getElementById('user-phone');
        const userPostcodeInput = document.getElementById('user-postcode');
        const signupBtn = document.getElementById('signup-btn');
        const signupSuccessEl = document.getElementById('signup-success');
        const userCodeEl = document.getElementById('user-code');
        const confirmationModal = document.getElementById('confirmation-modal');
        const modalConfirmBtn = document.getElementById('modal-confirm-btn');
        const modalCancelBtn = document.getElementById('modal-cancel-btn');

        let currentUserId = null;
        let isAdminLoggedIn = false;
        let eventsUnsubscribe = null;
        let allEvents = [];
        let confirmationCallback = null;

        // --- Modal Logic ---
        function showConfirmation(title, body, onConfirm) {
            document.getElementById('modal-title').textContent = title;
            document.getElementById('modal-body').textContent = body;
            confirmationCallback = onConfirm;
            confirmationModal.style.display = 'block';
        }
        modalConfirmBtn.addEventListener('click', () => {
            if (confirmationCallback) confirmationCallback();
            confirmationModal.style.display = 'none';
        });
        modalCancelBtn.addEventListener('click', () => {
            confirmationModal.style.display = 'none';
        });

        // --- Auth & Startup ---
        onAuthStateChanged(auth, async user => {
            if (user) {
                currentUserId = user.uid;
                await setupListeners();
                await checkAdminPasswordStatus();
            } else {
                try {
                    await (typeof __initial_auth_token !== 'undefined' ? signInWithCustomToken(auth, __initial_auth_token) : signInAnonymously(auth));
                } catch(error) {
                    console.error("Sign-in failed:", error);
                }
            }
            loadingEl.style.display = 'none';
            mainContentEl.style.display = 'block';
            showView('user');
        });

        async function checkAdminPasswordStatus() {
            try {
                const adminRef = doc(db, `artifacts/${appId}/public/data/admin/config`);
                const adminDoc = await getDoc(adminRef);
                adminSetupInfo.style.display = adminDoc.exists() ? 'none' : 'block';
            } catch (e) {
                console.error("Could not check admin status", e);
            }
        }

        // --- View Switching ---
        function showView(viewName) {
            [adminLoginSection, adminView, userView, forgotPasswordSection].forEach(el => el.style.display = 'none');
            if (viewName === 'adminLogin') adminLoginSection.style.display = 'block';
            else if (viewName === 'forgotPassword') forgotPasswordSection.style.display = 'block';
            else if (viewName === 'admin' && isAdminLoggedIn) adminView.style.display = 'block';
            else if (viewName === 'user') userView.style.display = 'block';
            else adminLoginSection.style.display = 'block';
        }

        showUserViewBtn.addEventListener('click', () => showView('user'));
        showAdminViewBtn.addEventListener('click', () => showView('adminLogin'));
        forgotPasswordLink.addEventListener('click', (e) => { e.preventDefault(); showView('forgotPassword'); });
        backToLoginLink.addEventListener('click', (e) => { e.preventDefault(); showView('adminLogin'); });

        // --- Admin Logic ---
        adminLoginBtn.addEventListener('click', async () => {
            const email = adminEmailInput.value.trim();
            const password = adminPasswordInput.value;
            if (!email || !password) return alert('Please enter both email and password.');

            try {
                const adminRef = doc(db, `artifacts/${appId}/public/data/admin/config`);
                const adminDoc = await getDoc(adminRef);

                if (!adminDoc.exists()) {
                    if (password.length < 6) return alert('Password must be at least 6 characters long.');
                    await setDoc(adminRef, { email, password });
                    isAdminLoggedIn = true;
                    showView('admin');
                    alert('Admin account created successfully. You are now logged in.');
                } else {
                    const adminData = adminDoc.data();
                    if (adminData.email === email && adminData.password === password) {
                        isAdminLoggedIn = true;
                        showView('admin');
                    } else {
                        alert('Incorrect email or password.');
                    }
                }
            } catch (error) {
                console.error("Error during admin login:", error);
                alert("An error occurred during login.");
            }
        });

        retrievePasswordBtn.addEventListener('click', async () => {
            const email = forgotPasswordEmailInput.value.trim();
            retrieveErrorEl.textContent = '';
            retrievedPasswordContainer.style.display = 'none';
            if (!email) return retrieveErrorEl.textContent = 'Please enter an email.';

            try {
                 const adminRef = doc(db, `artifacts/${appId}/public/data/admin/config`);
                 const adminDoc = await getDoc(adminRef);
                 if(adminDoc.exists() && adminDoc.data().email === email) {
                    retrievedPasswordEl.textContent = adminDoc.data().password;
                    retrievedPasswordContainer.style.display = 'block';
                 } else {
                    retrieveErrorEl.textContent = 'Admin email not found in our records.';
                 }
            } catch (error) {
                 console.error("Error retrieving password:", error);
                 retrieveErrorEl.textContent = 'An error occurred.';
            }
        });

        adminLogoutBtn.addEventListener('click', () => {
            isAdminLoggedIn = false;
            showView('adminLogin');
        });
        
        setPasswordBtn.addEventListener('click', async () => {
            const newPassword = newAdminPasswordInput.value;
            if (newPassword.length < 6) { alert('Password must be at least 6 characters long.'); return; }
            try {
                const adminRef = doc(db, `artifacts/${appId}/public/data/admin/config`);
                await updateDoc(adminRef, { password: newPassword });
                passwordStatusEl.textContent = 'Password updated successfully!';
                newAdminPasswordInput.value = '';
                setTimeout(() => { passwordStatusEl.textContent = '' }, 3000);
            } catch (error) {
                passwordStatusEl.textContent = 'Error setting password.';
            }
        });

        // --- Event Management ---
        addEventBtn.addEventListener('click', async () => {
            const name = eventNameInput.value.trim();
            const date = eventDateInput.value;
            if (!name || !date) { alert('Please provide event name and date.'); return; }
            try {
                await addDoc(collection(db, `artifacts/${appId}/public/data/events`), { name, date, attendees: {} });
                eventNameInput.value = '';
                eventDateInput.value = '';
                alert('Event added!');
            } catch (error) {
                alert('Failed to add event.');
            }
        });

        async function deleteEvent(eventId) {
            showConfirmation('Delete Event?', 'This will permanently delete the event and all its attendance data.', async () => {
                try {
                    await deleteDoc(doc(db, `artifacts/${appId}/public/data/events`, eventId));
                } catch (error) {
                    alert('Failed to delete event.');
                }
            });
        }
        
        async function removeUserFromEvent(eventId, userId) {
            showConfirmation('Remove User?', 'This will remove the user from this event\'s sign-up list.', async () => {
                 try {
                    await updateDoc(doc(db, `artifacts/${appId}/public/data/events`, eventId), { [`attendees.${userId}`]: deleteField() });
                } catch (error) {
                    alert('Failed to remove user.');
                }
            });
        }

        // --- User Registration ---
        signupBtn.addEventListener('click', async () => {
            const name = userNameInput.value.trim();
            const email = userEmailInput.value.trim();
            const phone = userPhoneInput.value.trim();
            const postcode = userPostcodeInput.value.trim().toUpperCase();

            if (!name || !phone || !email || !postcode) return alert('Please fill out all fields.');
            if (!/^\S+@\S+\.\S+$/.test(email)) return alert('Please enter a valid email address.');

            try {
                const usersCol = collection(db, `artifacts/${appId}/public/data/users`);
                const q = query(usersCol, where("phone", "==", phone));
                const querySnapshot = await getDocs(q);

                if (!querySnapshot.empty) return alert(`You are already registered! Your code is ${querySnapshot.docs[0].data().code}.`);

                const userCode = `EVT-${Math.random().toString(36).substr(2, 6).toUpperCase()}`;
                await addDoc(usersCol, { name, email, phone, postcode, code: userCode, attendanceCount: 0, attendedEvents: [] });
                
                [userNameInput, userPhoneInput, userEmailInput, userPostcodeInput].forEach(i => i.value = '');
                signupSuccessEl.style.display = 'block';
                userCodeEl.textContent = userCode;
                setTimeout(() => { signupSuccessEl.style.display = 'none'; }, 10000);

            } catch (error) {
                alert("Registration failed. Please try again.");
            }
        });
        
        async function markAttendance(eventId, userId, eventDate) {
            if (!isAdminLoggedIn) return { success: false, message: "Not logged in." };
            const eventRef = doc(db, `artifacts/${appId}/public/data/events`, eventId);
            const userRef = doc(db, `artifacts/${appId}/public/data/users`, userId);
            try {
                const eventDoc = await getDoc(eventRef);
                const userDoc = await getDoc(userRef);

                if (!eventDoc.exists() || !userDoc.exists()) return { success: false, message: "Event or User not found." };
                
                const eventData = eventDoc.data();
                const userData = userDoc.data();
                const eventAttendeeInfo = eventData.attendees[userId];

                if (!eventAttendeeInfo) return { success: false, message: `User not on attendee list. Use 'Manual Check-in' to add them.` };
                if (eventAttendeeInfo.attendedOn.includes(eventDate)) return { success: false, message: `User already marked present for this date.` };

                const firstTime = eventAttendeeInfo.attendedOn.length === 0;
                await updateDoc(eventRef, { [`attendees.${userId}.attendedOn`]: [...eventAttendeeInfo.attendedOn, eventDate] });

                if (firstTime) {
                    await updateDoc(userRef, {
                        attendanceCount: (userData.attendanceCount || 0) + 1,
                        attendedEvents: [...(userData.attendedEvents || []), { eventId, eventName: eventData.name, date: eventDate }]
                    });
                }
                return { success: true, message: `Attendance marked for ${userData.name}.` };
            } catch (error) {
                return { success: false, message: "Error marking attendance." };
            }
        }

        manualCheckinBtn.addEventListener('click', async () => {
            const userCode = manualCheckinCodeInput.value.trim().toUpperCase();
            const eventId = checkinEventSelect.value;
            checkinStatusEl.textContent = '';

            if (!eventId || !userCode) return checkinStatusEl.textContent = 'Please select an event and enter a code.';

            try {
                const q = query(collection(db, `artifacts/${appId}/public/data/users`), where("code", "==", userCode));
                const userSnapshot = await getDocs(q);

                if (userSnapshot.empty) return checkinStatusEl.textContent = 'No user found with this code.';

                const userDoc = userSnapshot.docs[0];
                const eventRef = doc(db, `artifacts/${appId}/public/data/events`, eventId);
                const eventDoc = await getDoc(eventRef);

                if (!eventDoc.exists()) return checkinStatusEl.textContent = 'Error: Event not found.';

                const eventData = eventDoc.data();
                if (!eventData.attendees || !eventData.attendees[userDoc.id]) {
                    const userData = userDoc.data();
                    await updateDoc(eventRef, {
                        [`attendees.${userDoc.id}`]: { name: userData.name, phone: userData.phone, code: userData.code, email: userData.email, postcode: userData.postcode, attendedOn: [] }
                    });
                }
                
                const result = await markAttendance(eventId, userDoc.id, eventData.date);
                checkinStatusEl.textContent = result.message;
                checkinStatusEl.className = `text-sm mt-2 ${result.success ? 'text-green-600' : 'text-red-600'}`;
                if (result.success) manualCheckinCodeInput.value = '';

            } catch (error) {
                checkinStatusEl.textContent = 'An error occurred during check-in.';
            } finally {
                setTimeout(() => { checkinStatusEl.textContent = ''; }, 5000);
            }
        });

        // --- Data Rendering ---
        function renderEvents(events) {
            allEvents = events;
            checkinEventSelect.innerHTML = '<option value="">-- Select an Event --</option>';
            events.forEach(event => {
                const option = document.createElement('option');
                option.value = event.id;
                option.textContent = `${event.name} (${event.date})`;
                checkinEventSelect.appendChild(option.cloneNode(true));
            });

            if (!isAdminLoggedIn) return;
            eventListsContainer.innerHTML = events.length === 0 ? '<p class="text-center text-gray-500">No events found. Create one above!</p>' : '';
            events.forEach(event => {
                const attendees = Object.entries(event.attendees || {});
                const attendeesHTML = attendees.length > 0 ? `
                    <div class="mt-4 flow-root"><div class="-mx-4 -my-2 overflow-x-auto sm:-mx-6 lg:-mx-8"><div class="inline-block min-w-full py-2 align-middle sm:px-6 lg:px-8">
                    <table class="min-w-full divide-y divide-gray-300"><thead><tr>
                        <th class="py-3.5 pl-4 pr-3 text-left text-sm font-semibold text-gray-900 sm:pl-0">Name & Phone</th>
                        <th class="px-3 py-3.5 text-left text-sm font-semibold text-gray-900">Code</th>
                        <th class="px-3 py-3.5 text-left text-sm font-semibold text-gray-900">Attendance</th>
                        <th class="relative py-3.5 pl-3 pr-4 sm:pr-0"><span class="sr-only">Actions</span></th>
                    </tr></thead><tbody class="divide-y divide-gray-200">
                    ${attendees.map(([userId, attendee]) => `
                        <tr>
                            <td class="whitespace-nowrap py-4 pl-4 pr-3 text-sm font-medium text-gray-900 sm:pl-0">${attendee.name} <span class="text-gray-500">(${attendee.phone})</span></td>
                            <td class="whitespace-nowrap px-3 py-4 text-sm text-gray-500">${attendee.code}</td>
                            <td class="whitespace-nowrap px-3 py-4 text-sm text-gray-500">${attendee.attendedOn ? attendee.attendedOn.length : 0} Day(s)</td>
                            <td class="relative whitespace-nowrap py-4 pl-3 pr-4 text-right text-sm font-medium sm:pr-0 space-x-4">
                                <button data-event-id="${event.id}" data-user-id="${userId}" data-event-date="${event.date}" class="mark-present-btn text-indigo-600 hover:text-indigo-900">Mark Present</button>
                                <button data-event-id="${event.id}" data-user-id="${userId}" class="remove-user-btn text-red-600 hover:text-red-900">Remove</button>
                            </td>
                        </tr>`).join('')}
                    </tbody></table></div></div></div>` : '<p class="text-gray-500 mt-4">No one has signed up yet.</p>';
                
                const eventEl = document.createElement('div');
                eventEl.className = 'bg-white p-6 rounded-lg shadow-md';
                eventEl.innerHTML = `<div class="flex justify-between items-start">
                    <div><h3 class="text-xl font-bold">${event.name}</h3><p class="text-sm text-gray-600">${event.date}</p><p class="text-lg font-semibold">${attendees.length} Signed Up</p></div>
                    <button data-event-id="${event.id}" class="delete-event-btn bg-red-100 text-red-700 hover:bg-red-600 hover:text-white text-sm font-medium py-1 px-3 rounded-md delete-btn">Delete Event</button>
                </div>${attendeesHTML}`;
                eventListsContainer.appendChild(eventEl);
            });
        }
        
        eventListsContainer.addEventListener('click', async e => {
            const button = e.target.closest('button');
            if (!button) return;
            const { eventId, userId, eventDate } = button.dataset;

            if (button.classList.contains('mark-present-btn')) {
                const result = await markAttendance(eventId, userId, eventDate);
                alert(result.message);
            } else if (button.classList.contains('remove-user-btn')) {
                removeUserFromEvent(eventId, userId);
            } else if (button.classList.contains('delete-event-btn')) {
                deleteEvent(eventId);
            }
        });

        async function setupListeners() {
            if (!currentUserId) return;
            if (eventsUnsubscribe) eventsUnsubscribe();
            const eventsQuery = query(collection(db, `artifacts/${appId}/public/data/events`));
            eventsUnsubscribe = onSnapshot(eventsQuery, (snapshot) => {
                const events = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
                events.sort((a,b) => new Date(b.date) - new Date(a.date));
                renderEvents(events);
            }, (error) => console.error("Error listening to events collection:", error));
        }
    </script>
</body>
</html>
