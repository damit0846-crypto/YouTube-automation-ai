# YouTube-automation-ai
require("dotenv").config();
const express = require("express");
const path = require("path");
const { spawn } = require("child_process");
const app = express();
const PORT = process.env.PORT || 3000;
let running = false;
let lastLog = "Ready. Set your .env values, then click Run automation.";
app.use(express.json());
app.use(express.static(path.join(__dirname, "public")));
app.get("/api/status", (_req, res) => res.json({ running, log: lastLog }));
app.post("/api/run", (_req, res) => {
  if (running) return res.status(409).json({ error: "Automation is already running." });
  running = true;
  lastLog = "Starting automation...";
  const child = spawn("node", ["automation.js"], { cwd: __dirname, env: process.env });
  child.stdout.on("data", d => { lastLog += d.toString(); });
  child.stderr.on("data", d => { lastLog += d.toString(); });
  child.on("close", code => {
    running = false;
    lastLog += `\nFinished with exit code ${code}`;
  });
  res.json({ ok: true, message: "Automation started. Refresh status to see logs." });
});
app.listen(PORT, () => console.log(`AI Future Lab dashboard: http://localhost:${PORT}`));
