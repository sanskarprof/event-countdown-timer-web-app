<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Event Countdown Timer</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Poppins Font -->
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Poppins', sans-serif;
            background-color: #1a1a1a; /* Dark black background */
        }
        /* Custom scrollbar for event list */
        .event-list-container::-webkit-scrollbar {
            width: 8px;
        }
        .event-list-container::-webkit-scrollbar-track {
            background: #333; /* Darker track */
            border-radius: 10px;
        }
        .event-list-container::-webkit-scrollbar-thumb {
            background: #880000; /* Dark red thumb */
            border-radius: 10px;
        }
        .event-list-container::-webkit-scrollbar-thumb:hover {
            background: #ff0000; /* Brighter red on hover */
        }
        .hidden {
            display: none;
        }
    </style>
</head>
<body class="min-h-screen flex items-center justify-center p-4">
    <div class="bg-gray-900 p-8 rounded-xl shadow-lg w-full max-w-2xl border border-gray-700">
        <h1 class="text-4xl font-extrabold text-center text-red-500 mb-6 tracking-tight">
            Event Countdown Timer
        </h1>

        <!-- Authentication Section -->
        <div id="authSection" class="mb-8 p-6 bg-gradient-to-br from-gray-800 to-black rounded-lg shadow-inner border border-gray-700">
            <h2 class="text-2xl font-bold text-red-400 mb-4 text-center">Welcome!</h2>
            <p class="text-gray-300 text-center mb-6">Sign in or create an account to save your events across devices.</p>

            <div class="space-y-4">
                <input type="email" id="authEmail" placeholder="Email"
                       class="w-full p-3 border border-gray-600 rounded-lg focus:outline-none focus:ring-2 focus:ring-red-500 transition duration-200 text-white bg-gray-700"
                       aria-label="Email Address">
                <input type="password" id="authPassword" placeholder="Password"
                       class="w-full p-3 border border-gray-600 rounded-lg focus:outline-none focus:ring-2 focus:ring-red-500 transition duration-200 text-white bg-gray-700"
                       aria-label="Password">
                <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <button id="signUpBtn"
                            class="bg-red-600 hover:bg-red-700 text-white font-bold py-3 px-6 rounded-lg shadow-md hover:shadow-lg transition duration-300 ease-in-out transform hover:-translate-y-0.5 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2">
                        Sign Up
                    </button>
                    <button id="signInBtn"
                            class="bg-red-800 hover:bg-red-900 text-white font-bold py-3 px-6 rounded-lg shadow-md hover:shadow-lg transition duration-300 ease-in-out transform hover:-translate-y-0.5 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2">
                        Sign In
                    </button>
                </div>
                <button id="guestSignInBtn"
                        class="w-full bg-gray-700 hover:bg-gray-600 text-white font-bold py-3 px-6 rounded-lg shadow-md hover:shadow-lg transition duration-300 ease-in-out transform hover:-translate-y-0.5 focus:outline-none focus:ring-2 focus:ring-gray-500 focus:ring-offset-2">
                    Continue as Guest
                </button>
            </div>
        </div>

        <!-- Main Application Content -->
        <div id="appContent" class="hidden">
            <!-- User ID Display -->
            <div class="mb-6 bg-red-950 border border-red-900 text-red-300 p-3 rounded-lg flex items-center justify-between">
                <span class="font-medium">Your User ID:</span>
                <span id="userIdDisplay" class="font-mono text-sm break-all ml-2">Loading...</span>
                <button id="signOutBtn"
                        class="ml-4 bg-gray-700 hover:bg-gray-600 text-white text-sm font-bold py-1 px-3 rounded-lg shadow-md transition duration-200">
                    Sign Out
                </button>
            </div>

            <!-- Add New Event Form -->
            <div class="mb-8 p-6 bg-gradient-to-br from-gray-800 to-black rounded-lg shadow-inner border border-gray-700">
                <h2 class="text-2xl font-bold text-red-400 mb-4">Add New Event</h2>
                <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                    <input type="text" id="eventName" placeholder="Event Name (e.g., Exam, Birthday)"
                           class="col-span-1 md:col-span-2 p-3 border border-gray-600 rounded-lg focus:outline-none focus:ring-2 focus:ring-red-500 transition duration-200 text-white bg-gray-700"
                           aria-label="Event Name">
                    <input type="date" id="eventDate"
                           class="p-3 border border-gray-600 rounded-lg focus:outline-none focus:ring-2 focus:ring-red-500 transition duration-200 text-white bg-gray-700"
                           aria-label="Event Date">
                    <button id="addEventBtn"
                            class="col-span-1 md:col-span-3 bg-red-600 hover:bg-red-700 text-white font-bold py-3 px-6 rounded-lg shadow-md hover:shadow-lg transition duration-300 ease-in-out transform hover:-translate-y-0.5 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2">
                        Add Event
                    </button>
                </div>
            </div>

            <!-- Event List Section -->
            <div>
                <h2 class="text-2xl font-bold text-red-400 mb-4">Your Countdowns</h2>
                <div id="eventList" class="space-y-4 event-list-container max-h-96 overflow-y-auto pr-2">
                    <!-- Countdown events will be dynamically loaded here -->
                    <p id="noEventsMessage" class="text-gray-400 text-center py-4">No events added yet. Add one above!</p>
                </div>
            </div>
        </div>
    </div>

    <!-- Custom Modal for Messages (instead of alert/confirm) -->
    <div id="customModal" class="fixed inset-0 bg-gray-900 bg-opacity-75 flex items-center justify-center z-50 hidden">
        <div class="bg-gray-800 p-6 rounded-lg shadow-xl max-w-sm w-full text-center">
            <p id="modalMessage" class="text-lg font-semibold text-white mb-4"></p>
            <button id="modalCloseBtn" class="bg-red-600 hover:bg-red-700 text-white font-bold py-2 px-4 rounded-lg transition duration-200">
                OK
            </button>
        </div>
    </div>

    <!-- Firebase SDKs -->
    <script type="module">
        // Import Firebase modules
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken, createUserWithEmailAndPassword, signInWithEmailAndPassword, signOut, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-auth.js";
        import { getFirestore, collection, addDoc, doc, deleteDoc, onSnapshot, query, where } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-firestore.js";

        // Global variables for Firebase instances
        let app;
        let db;
        let auth;
        let userId = 'loading'; // Default to loading state
        let unsubscribeFromEvents = null; // To store the unsubscribe function for Firestore listener

        // Get app ID from the environment (can be a fixed string for external deployment)
        const appId = 'event-countdown-app-project'; // You can choose any unique string here for your project

        // YOUR FIREBASE PROJECT'S CONFIGURATION
        const firebaseConfig = {
            apiKey: "AIzaSyCpZd4WD-pGMZgB0uWrQCDJATgz5TUroLQ",
            authDomain: "event-scheduler-60757.firebaseapp.com",
            projectId: "event-scheduler-60757",
            storageBucket: "event-scheduler-60757.firebasestorage.app",
            messagingSenderId: "80427425452",
            appId: "1:80427425452:web:35ef025f2fa70625dbc909",
            measurementId: "G-53CJTVDQDP"
        };

        // UI Elements
        const authSection = document.getElementById('authSection');
        const appContent = document.getElementById('appContent');
        const authEmailInput = document.getElementById('authEmail');
        const authPasswordInput = document.getElementById('authPassword');
        const signUpBtn = document.getElementById('signUpBtn');
        const signInBtn = document.getElementById('signInBtn');
        const guestSignInBtn = document.getElementById('guestSignInBtn');
        const signOutBtn = document.getElementById('signOutBtn');

        const eventNameInput = document.getElementById('eventName');
        const eventDateInput = document.getElementById('eventDate');
        const addEventBtn = document.getElementById('addEventBtn');
        const eventListDiv = document.getElementById('eventList');
        const noEventsMessage = document.getElementById('noEventsMessage');
        const userIdDisplay = document.getElementById('userIdDisplay');

        // Modal Elements
        const customModal = document.getElementById('customModal');
        const modalMessage = document.getElementById('modalMessage');
        const modalCloseBtn = document.getElementById('modalCloseBtn');

        // Function to show custom modal
        function showModal(message) {
            modalMessage.textContent = message;
            customModal.classList.remove('hidden');
        }

        // Function to hide custom modal
        modalCloseBtn.addEventListener('click', () => {
            customModal.classList.add('hidden');
        });

        // Initialize Firebase
        async function initializeFirebase() {
            try {
                app = initializeApp(firebaseConfig);
                db = getFirestore(app);
                auth = getAuth(app);

                console.log("Firebase Config (from code):", firebaseConfig);

                // Listen for auth state changes to manage UI and user ID
                onAuthStateChanged(auth, (user) => {
                    if (user) {
                        userId = user.uid;
                        userIdDisplay.textContent = userId;
                        console.log("User authenticated with ID:", userId, "isAnonymous:", user.isAnonymous);
                        authSection.classList.add('hidden'); // Hide auth section
                        appContent.classList.remove('hidden'); // Show app content
                        setupRealtimeEventsListener(); // Start listening for events for this user
                    } else {
                        userId = 'not-authenticated';
                        userIdDisplay.textContent = 'N/A'; // Clear user ID display
                        console.log("User is not authenticated.");
                        authSection.classList.remove('hidden'); // Show auth section
                        appContent.classList.add('hidden'); // Hide app content
                        // Clear existing events and intervals if user logs out
                        eventListDiv.innerHTML = '';
                        noEventsMessage.classList.remove('hidden');
                        eventListDiv.appendChild(noEventsMessage);
                        for (const key in countdownIntervals) {
                            clearInterval(countdownIntervals[key]);
                            delete countdownIntervals[key];
                        }
                        if (unsubscribeFromEvents) {
                            unsubscribeFromEvents(); // Unsubscribe from previous user's events
                            unsubscribeFromEvents = null;
                        }
                    }
                });

                // In a local environment, __initial_auth_token is not available.
                // The user will explicitly choose to Sign Up, Sign In, or Continue as Guest.
                // No automatic sign-in attempt here to allow the auth section to be visible.

            } catch (error) {
                console.error("Error initializing Firebase:", error);
                showModal("Failed to initialize the app. Please check your Firebase configuration and ensure it's correct.");
            }
        }

        // --- Authentication Functions ---

        async function handleSignUp() {
            const email = authEmailInput.value;
            const password = authPasswordInput.value;

            if (!email || !password) {
                showModal("Please enter both email and password to sign up.");
                return;
            }
            if (password.length < 6) {
                showModal("Password should be at least 6 characters long.");
                return;
            }

            try {
                await createUserWithEmailAndPassword(auth, email, password);
                showModal("Account created successfully! You are now signed in.");
                authEmailInput.value = '';
                authPasswordInput.value = '';
            } catch (error) {
                console.error("Error signing up:", error);
                let errorMessage = "Failed to create account. This usually means Email/Password sign-in is NOT enabled in your Firebase project. Please check your Firebase Console.";
                if (error.code === 'auth/email-already-in-use') {
                    errorMessage = "Email is already in use. Try signing in or use a different email.";
                } else if (error.code === 'auth/invalid-email') {
                    errorMessage = "Invalid email address.";
                } else if (error.code === 'auth/network-request-failed') {
                    errorMessage = "Network error. Please check your internet connection.";
                }
                showModal(errorMessage);
            }
        }

        async function handleSignIn() {
            const email = authEmailInput.value;
            const password = authPasswordInput.value;

            if (!email || !password) {
                showModal("Please enter both email and password to sign in.");
                return;
            }

            try {
                await signInWithEmailAndPassword(auth, email, password);
                showModal("Signed in successfully!");
                authEmailInput.value = '';
                authPasswordInput.value = '';
            } catch (error) {
                console.error("Error signing in:", error);
                let errorMessage = "Failed to sign in. This usually means Email/Password sign-in is NOT enabled in your Firebase project, or invalid credentials. Please check your Firebase Console.";
                if (error.code === 'auth/user-not-found' || error.code === 'auth/wrong-password' || error.code === 'auth/invalid-credential') {
                    errorMessage = "Invalid email or password.";
                } else if (error.code === 'auth/invalid-email') {
                    errorMessage = "Invalid email address.";
                } else if (error.code === 'auth/network-request-failed') {
                    errorMessage = "Network error. Please check your internet connection.";
                }
                showModal(errorMessage);
            }
        }

        async function handleGuestSignIn() {
            try {
                await signInAnonymously(auth);
                showModal("Signed in as guest. Your events will be saved on this device.");
            } catch (error) {
                console.error("Error signing in anonymously:", error);
                let errorMessage = "Failed to sign in as guest. Please ensure Anonymous sign-in is enabled in your Firebase project.";
                if (error.code === 'auth/network-request-failed') {
                    errorMessage = "Network error. Please check your internet connection.";
                }
                showModal(errorMessage);
            }
        }

        async function handleSignOut() {
            try {
                await signOut(auth);
                showModal("You have been signed out.");
            } catch (error) {
                console.error("Error signing out:", error);
                showModal("Failed to sign out. Please try again.");
            }
        }

        // --- Event Countdown Logic ---

        // Function to calculate time remaining
        function getTimeRemaining(targetDate) {
            const now = new Date().getTime();
            const distance = targetDate - now;

            if (distance < 0) {
                return { days: 0, hours: 0, minutes: 0, seconds: 0, expired: true };
            }

            const days = Math.floor(distance / (1000 * 60 * 60 * 24));
            const hours = Math.floor((distance % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
            const minutes = Math.floor((distance % (1000 * 60 * 60)) / (1000 * 60));
            const seconds = Math.floor((distance % (1000 * 60)) / 1000);

            return { days, hours, minutes, seconds, expired: false };
        }

        // Store countdown intervals to clear them later
        const countdownIntervals = {};

        // Function to render/update a single event card
        function renderEventCard(event) {
            const cardId = `event-card-${event.id}`;
            let card = document.getElementById(cardId);

            if (!card) {
                // Create new card if it doesn't exist
                card = document.createElement('div');
                card.id = cardId;
                card.className = 'bg-gray-800 p-5 rounded-lg shadow-sm border border-gray-700 flex flex-col md:flex-row items-start md:items-center justify-between transition-all duration-300 transform hover:scale-[1.01]';
                eventListDiv.appendChild(card);
            }

            // Clear existing interval for this card if any
            if (countdownIntervals[event.id]) {
                clearInterval(countdownIntervals[event.id]);
            }

            const updateCountdown = () => {
                const targetDate = new Date(event.targetDate).getTime();
                const { days, hours, minutes, seconds, expired } = getTimeRemaining(targetDate);

                let countdownText = '';
                let countdownColor = 'text-white'; // Default color for countdown

                if (expired) {
                    countdownText = 'Event has passed!';
                    countdownColor = 'text-red-500 font-semibold';
                    clearInterval(countdownIntervals[event.id]); // Stop updating if expired
                } else {
                    countdownText = `${days}d ${hours}h ${minutes}m ${seconds}s`;
                    if (days < 1 && hours < 12) { // Less than 12 hours remaining
                        countdownColor = 'text-yellow-400 font-semibold'; /* Changed from orange to yellow for contrast */
                    }
                    if (days === 0 && hours === 0 && minutes < 60) { // Less than 1 hour remaining
                        countdownColor = 'text-red-500 font-bold';
                    }
                }

                card.innerHTML = `
                    <div class="flex-grow mb-4 md:mb-0">
                        <h3 class="text-xl font-semibold text-white">${event.name}</h3>
                        <p class="text-sm text-gray-400">Target Date: ${new Date(event.targetDate).toLocaleDateString()}</p>
                        <p class="text-2xl font-bold ${countdownColor}" id="countdown-${event.id}">${countdownText}</p>
                    </div>
                    <button data-id="${event.id}"
                            class="delete-btn bg-red-500 hover:bg-red-600 text-white font-bold py-2 px-4 rounded-lg shadow-md transition duration-200 ease-in-out transform hover:scale-105 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2">
                        Delete
                    </button>
                `;

                // Add event listener to the new delete button
                card.querySelector('.delete-btn').addEventListener('click', handleDeleteEvent);
            };

            // Initial update
            updateCountdown();
            // Update every second
            countdownIntervals[event.id] = setInterval(updateCountdown, 1000);
        }

        // Function to handle adding a new event
        async function handleAddEvent() {
            if (userId === 'not-authenticated') {
                showModal("Please sign in or continue as a guest to add events.");
                return;
            }

            const name = eventNameInput.value.trim();
            const dateString = eventDateInput.value;

            if (!name || !dateString) {
                showModal("Please enter both event name and date.");
                return;
            }

            const targetDate = new Date(dateString);
            if (isNaN(targetDate.getTime())) {
                showModal("Invalid date format. Please select a valid date.");
                return;
            }

            if (targetDate.getTime() < new Date().getTime()) {
                showModal("Please select a future date for the event.");
                return;
            }

            try {
                // Path for private user data
                const eventsCollectionRef = collection(db, `artifacts/${appId}/users/${userId}/events`);
                await addDoc(eventsCollectionRef, {
                    name: name,
                    targetDate: targetDate.toISOString(), // Store as ISO string for consistent parsing
                    createdAt: new Date().toISOString()
                });
                eventNameInput.value = '';
                eventDateInput.value = '';
                showModal("Event added successfully!");
            } catch (e) {
                console.error("Error adding document: ", e);
                showModal("Failed to add event. Please try again.");
            }
        }

        // Function to handle deleting an event
        async function handleDeleteEvent(event) {
            if (userId === 'not-authenticated') {
                showModal("Please sign in or continue as a guest to delete events.");
                return;
            }

            const eventId = event.target.dataset.id;
            if (!eventId) {
                console.error("No event ID found for deletion.");
                return;
            }

            // Replacing browser confirm with custom modal for consistency
            showModal("Are you sure you want to delete this event?");
            modalCloseBtn.onclick = async () => { // Reassign onclick for confirmation
                customModal.classList.add('hidden');
                try {
                    // Path for private user data
                    const eventDocRef = doc(db, `artifacts/${appId}/users/${userId}/events`, eventId);
                    await deleteDoc(eventDocRef);
                    showModal("Event deleted successfully!");
                } catch (e) {
                    console.error("Error deleting document: ", e);
                    showModal("Failed to delete event. Please try again.");
                }
                // Reset modalCloseBtn's original listener
                modalCloseBtn.onclick = () => customModal.classList.add('hidden');
            };
        }

        // Set up real-time listener for events
        function setupRealtimeEventsListener() {
            if (!db || !userId || userId === 'loading' || userId === 'not-authenticated') {
                console.log("Firestore or userId not ready, delaying listener setup.");
                return;
            }

            // If there's an existing listener, unsubscribe first to avoid duplicates
            if (unsubscribeFromEvents) {
                unsubscribeFromEvents();
            }

            // Path for private user data
            const eventsCollectionRef = collection(db, `artifacts/${appId}/users/${userId}/events`);

            unsubscribeFromEvents = onSnapshot(eventsCollectionRef, (snapshot) => {
                const events = [];
                snapshot.forEach(doc => {
                    events.push({ id: doc.id, ...doc.data() });
                });

                // Sort events by target date
                events.sort((a, b) => new Date(a.targetDate).getTime() - new Date(b.targetDate).getTime());

                // Clear existing intervals and content before re-rendering
                for (const key in countdownIntervals) {
                    clearInterval(countdownIntervals[key]);
                    delete countdownIntervals[key];
                }
                eventListDiv.innerHTML = '';

                if (events.length === 0) {
                    noEventsMessage.classList.remove('hidden');
                    eventListDiv.appendChild(noEventsMessage);
                } else {
                    noEventsMessage.classList.add('hidden');
                    events.forEach(event => renderEventCard(event));
                }
            }, (error) => {
                console.error("Error fetching real-time events:", error);
                showModal("Failed to load events. Please check your connection and Firebase security rules.");
            });
        }

        // Event Listeners
        signUpBtn.addEventListener('click', handleSignUp);
        signInBtn.addEventListener('click', handleSignIn);
        guestSignInBtn.addEventListener('click', handleGuestSignIn);
        signOutBtn.addEventListener('click', handleSignOut);
        addEventBtn.addEventListener('click', handleAddEvent);

        // Initialize Firebase when the window loads
        window.onload = initializeFirebase;
    </script>
</body>
</html>
