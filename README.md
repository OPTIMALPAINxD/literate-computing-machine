const express = require("express");
const http = require("http");
const socketIo = require("socket.io");
const cors = require("cors");

const app = express();
app.use(cors());

const server = http.createServer(app);
const io = socketIo(server, {
    cors: { origin: "*" }
});

let users = {};

io.on("connection", (socket) => {
    console.log("User connected:", socket.id);

    socket.on("register", (userId) => {
        users[userId] = socket.id;
    });

    socket.on("sendMessage", ({ to, message }) => {
        if (users[to]) {
            io.to(users[to]).emit("receiveMessage", message);
        }
    });

    socket.on("disconnect", () => {
        console.log("Disconnected");
    });
});

server.listen(3000, () => console.log("Server running"));
