<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Community Noticeboard</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f4;
            margin: 0;
            padding: 0;
        }

        .tabs {
            background-color: #007bff;
            padding: 10px;
            text-align: center;
        }

        .tabs button {
            background-color: #008cba;
            padding: 10px 20px;
            margin: 0 10px;
            border: none;
            color: white;
            cursor: pointer;
        }

        .tabs button:hover {
            background-color: #007bff;
        }

        .tab-content {
            display: none;
            background-color: white;
            padding: 20px;
            margin-top: 20px;
            border-radius: 5px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        }

        .active {
            display: block;
        }

        h2 {
            color: #333;
        }

        form {
            margin-bottom: 20px;
        }

        label {
            display: block;
            margin-top: 10px;
            color: #555;
        }

        input, textarea {
            width: 100%;
            padding: 10px;
            margin: 5px 0;
            border: 1px solid #ccc;
            border-radius: 5px;
            box-sizing: border-box;
        }

        button {
            padding: 10px 15px;
            background-color: #28a745;
            border: none;
            color: white;
            border-radius: 5px;
            cursor: pointer;
        }

        button:hover {
            background-color: #218838;
        }

        .post {
            border-bottom: 1px solid #ddd;
            padding: 15px 0;
        }

        .post h3 {
            margin: 0;
            color: #333;
        }

        .post p {
            color: #555;
        }

        #passwordInput {
            display: none;
        }

        .password-form label,
        .password-form input {
            display: block;
            margin-top: 10px;
        }

        .password-form input {
            width: 60%;
        }

        .comments-section {
            margin-top: 10px;
        }

        .comment {
            margin-top: 10px;
            padding-left: 15px;
            border-left: 2px solid #ccc;
        }

        .upvote-btn {
            cursor: pointer;
            background-color: #28a745;
            padding: 5px 10px;
            border: none;
            color: white;
            border-radius: 5px;
            margin: 10px 0;
        }

        .upvote-btn:hover {
            background-color: #218838;
        }

        .upvote-count {
            font-size: 14px;
            color: #555;
        }

        .view-comments-btn {
            margin-top: 10px;
            padding: 5px 15px;
            background-color: #007bff;
            border: none;
            color: white;
            border-radius: 5px;
        }

        .view-comments-btn:hover {
            background-color: #0056b3;
        }

    </style>
</head>
<body>
    <div class="tabs">
        <button onclick="showTab('notices')">Notices</button>
        <button onclick="showTab('hero')">Hero's Help Page</button>
        <button onclick="showTab('community')">Community Notes</button>
    </div>

    <!-- Community Notes Tab -->
    <div id="community" class="tab-content">
        <h2>Community Notes</h2>
        <form id="addCommunityPostForm">
            <label for="postTitle">Title:</label>
            <input type="text" id="postTitle" required>

            <label for="postContent">Content:</label>
            <textarea id="postContent" required></textarea>
            <button type="submit">Add Post</button>
        </form>
        <div id="communityPosts"></div>
    </div>

    <!-- Hero's Help Page Tab -->
    <div id="hero" class="tab-content">
        <h2>Hero's Help Page</h2>
        <div id="passwordDivHero" class="password-form">
            <label for="passwordInputHero">Enter Password to Add Post:</label>
            <input type="password" id="passwordInputHero" placeholder="Password" />
            <button onclick="checkPasswordHero()">Submit Password</button>
        </div>
        <div id="heroPosts"></div>
        <form id="addHeroPostForm" style="display:none;">
            <label for="heroPostTitle">Title:</label>
            <input type="text" id="heroPostTitle" required>

            <label for="heroPostContent">Content:</label>
            <textarea id="heroPostContent" required></textarea>
            <button type="submit">Add Hero Post</button>
        </form>
    </div>

    <!-- Notices Tab -->
    <div id="notices" class="tab-content">
        <h2>Notices</h2>
        <div id="passwordDiv" class="password-form">
            <label for="passwordInput">Enter Password:</label>
            <input type="password" id="passwordInput" placeholder="Password" />
            <button onclick="checkPassword()">Submit Password</button>
        </div>
        <div id="noticesList">
            <!-- Notices will appear here -->
        </div>
        <form id="addNoticeForm" style="display:none;">
            <label for="noticeTitle">Title:</label>
            <input type="text" id="noticeTitle" required>

            <label for="noticeContent">Content:</label>
            <textarea id="noticeContent" required></textarea>
            <button type="submit">Add Notice</button>
        </form>
    </div>

    <script>
        // Posts storage (this should normally come from a backend or database)
        const communityPosts = [];
        const heroPosts = [];
        const noticesPosts = [];

        // Add post to Community Notes
        document.getElementById('addCommunityPostForm').addEventListener('submit', function(event) {
            event.preventDefault();
            const title = document.getElementById('postTitle').value;
            const content = document.getElementById('postContent').value;
            communityPosts.push({ title, content, upvotes: 0, comments: [], commentsVisible: false });
            document.getElementById('postTitle').value = '';
            document.getElementById('postContent').value = '';
            updateCommunityPosts();
        });

        // Add post to Hero's Help Page
        document.getElementById('addHeroPostForm').addEventListener('submit', function(event) {
            event.preventDefault();
            const title = document.getElementById('heroPostTitle').value;
            const content = document.getElementById('heroPostContent').value;
            heroPosts.push({ title, content });
            document.getElementById('heroPostTitle').value = '';
            document.getElementById('heroPostContent').value = '';
            updateHeroPosts();
        });

        // Add post to Notices
        document.getElementById('addNoticeForm').addEventListener('submit', function(event) {
            event.preventDefault();
            const title = document.getElementById('noticeTitle').value;
            const content = document.getElementById('noticeContent').value;
            noticesPosts.push({ title, content });
            document.getElementById('noticeTitle').value = '';
            document.getElementById('noticeContent').value = '';
            updateNoticesPosts();
        });

        // Update Community Posts
        function updateCommunityPosts() {
            const container = document.getElementById('communityPosts');
            container.innerHTML = ''; // Clear existing posts
            communityPosts.forEach((post, index) => {
                const postElement = document.createElement('div');
                postElement.classList.add('post');
                postElement.innerHTML = `
                    <h3>${post.title}</h3>
                    <p>${post.content}</p>
                    <button class="upvote-btn" onclick="upvotePost(${index})">Upvote</button>
                    <span class="upvote-count">Upvotes: ${post.upvotes}</span>
                    <button class="view-comments-btn" onclick="toggleComments(${index})">View Comments</button>
                    ${post.commentsVisible ? 
                    `<div class="comments-section">
                        <label for="commentInput-${index}">Add Comment:</label>
                        <textarea id="commentInput-${index}" placeholder="Write your comment here"></textarea>
                        <button onclick="addComment(${index})">Add Comment</button>
                        ${post.comments.map(comment => `<div class="comment">${comment}</div>`).join('')}
                    </div>` : ''}
                `;
                container.appendChild(postElement);
            });
        }

        // Toggle visibility of comments
        function toggleComments(postIndex) {
            const post = communityPosts[postIndex];
            post.commentsVisible = !post.commentsVisible;
            updateCommunityPosts();
        }

        // Upvote a Community Post
        function upvotePost(postIndex) {
            communityPosts[postIndex].upvotes++;
            updateCommunityPosts();
        }

        // Add comment to a post
        function addComment(postIndex) {
            const commentText = document.getElementById(`commentInput-${postIndex}`).value;
            if (commentText) {
                communityPosts[postIndex].comments.push(commentText);
                document.getElementById(`commentInput-${postIndex}`).value = ''; // Clear the input
                updateCommunityPosts();
            }
        }

        // Update Hero's Help Page Posts
        function updateHeroPosts() {
            const container = document.getElementById('heroPosts');
            container.innerHTML = ''; // Clear existing posts
            heroPosts.forEach(post => {
                const postElement = document.createElement('div');
                postElement.classList.add('post');
                postElement.innerHTML = `<h3>${post.title}</h3><p>${post.content}</p>`;
                container.appendChild(postElement);
            });
        }

        // Update Notices
        function updateNoticesPosts() {
            const container = document.getElementById('noticesList');
            container.innerHTML = ''; // Clear existing posts
            noticesPosts.forEach(post => {
                const postElement = document.createElement('div');
                postElement.classList.add('post');
                postElement.innerHTML = `<h3>${post.title}</h3><p>${post.content}</p>`;
                container.appendChild(postElement);
            });
        }

        // Function to check password and show Hero's Help Page form
        function checkPasswordHero() {
            const correctPasswordHero = "hero123";  // Set your password here for Hero's Help Page
            const enteredPasswordHero = document.getElementById('passwordInputHero').value;

            if (enteredPasswordHero === correctPasswordHero) {
                document.getElementById('passwordDivHero').style.display = 'none';
                document.getElementById('addHeroPostForm').style.display = 'block'; // Show the form for adding a Hero Post
            } else {
                alert("Incorrect password for Hero's Help Page!");
            }
        }

        // Function to check password and show Notices form
        function checkPassword() {
            const correctPassword = "admin123";  // Set your password here
            const enteredPassword = document.getElementById('passwordInput').value;

            if (enteredPassword === correctPassword) {
                document.getElementById('passwordDiv').style.display = 'none';
                document.getElementById('addNoticeForm').style.display = 'block'; // Show the form for adding a Notice
            } else {
                alert("Incorrect password for Notices!");
            }
        }

        // Function to switch between tabs
        function showTab(tabName) {
            const tabs = document.querySelectorAll('.tab-content');
            tabs.forEach(tab => tab.classList.remove('active'));
            document.getElementById(tabName).classList.add('active');
        }

        // Default tab to show on page load
        showTab('community');
    </script>
</body>
</html>
