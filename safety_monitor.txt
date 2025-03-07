#include <WiFi.h>
#include <WebServer.h>

const char* ssid = "OnePlus 11R 5G";  // Replace with your WiFi SSID
const char* password = "mugil.v8";  // Replace with your WiFi Password

WebServer server(80);

// Sensor pins
const int gasSensorPin = 34;  // MQ2 gas sensor for Worker 1
const int heartRatePin = 35;  // Heart rate sensor for Worker 1

// Threshold values
const int GAS_THRESHOLD = 400;
const int HEART_RATE_THRESHOLD = 120;

// Historical data storage (last 10 readings)
const int historySize = 10;
int gasHistory[historySize] = {0};
int heartRateHistory[historySize] = {0};
int historyIndex = 0;

// Function to generate the dashboard with a graph
void handleRoot() {
    String html = "<!DOCTYPE html><html><head>";
    html += "<meta name=\"viewport\" content=\"width=device-width, initial-scale=1\">";
    html += "<script src=\"https://cdn.jsdelivr.net/npm/chart.js\"></script>";  // Include Chart.js
    html += "<style>";
    html += "body { font-family: Arial, sans-serif; background: #f4f4f4; text-align: center; }";
    html += ".container { width: 80%; margin: auto; background: white; padding: 20px; border-radius: 10px; box-shadow: 0px 0px 10px rgba(0,0,0,0.2); }";
    html += "canvas { max-width: 100%; }";
    html += "</style>";

    html += "<script>";
    html += "let gasChart, heartRateChart;";
    html += "function updateData() {";
    html += "  var xhttp = new XMLHttpRequest();";
    html += "  xhttp.onreadystatechange = function() {";
    html += "    if (this.readyState == 4 && this.status == 200) {";
    html += "      var data = JSON.parse(this.responseText);";
    html += "      document.getElementById('heartRate').innerHTML = data.current.heartRate;";
    html += "      document.getElementById('gasValue').innerHTML = data.current.gas;";
    html += "      document.getElementById('status').innerHTML = data.current.status;";
    html += "      document.getElementById('status').style.color = data.current.statusColor;";
    
    // Update chart data
    html += "      let labels = Array.from({length: data.history.length}, (_, i) => 'Reading ' + (i + 1));";
    html += "      gasChart.data.labels = labels;";
    html += "      gasChart.data.datasets[0].data = data.history.map(d => d.gas);";
    html += "      gasChart.update();";
    
    html += "      heartRateChart.data.labels = labels;";
    html += "      heartRateChart.data.datasets[0].data = data.history.map(d => d.heartRate);";
    html += "      heartRateChart.update();";
    
    html += "    }";
    html += "  };";
    html += "  xhttp.open('GET', '/sensor', true);";
    html += "  xhttp.send();";
    html += "}";

    html += "document.addEventListener('DOMContentLoaded', function() {";
    html += "  let ctx1 = document.getElementById('gasChart').getContext('2d');";
    html += "  gasChart = new Chart(ctx1, { type: 'line', data: { labels: [], datasets: [{ label: 'Gas Level', borderColor: 'red', backgroundColor: 'rgba(255, 0, 0, 0.2)', data: [] }] }, options: { responsive: true, scales: { y: { beginAtZero: true } } } });";
    
    html += "  let ctx2 = document.getElementById('heartRateChart').getContext('2d');";
    html += "  heartRateChart = new Chart(ctx2, { type: 'line', data: { labels: [], datasets: [{ label: 'Heart Rate', borderColor: 'blue', backgroundColor: 'rgba(0, 0, 255, 0.2)', data: [] }] }, options: { responsive: true, scales: { y: { beginAtZero: true } } } });";
    
    html += "  updateData(); setInterval(updateData, 2000);";  // Auto-refresh every 2 seconds
    html += "});";
    html += "</script>";

    html += "</head><body>";
    html += "<div class='container'>";
    html += "<h1>Mine Worker Safety Dashboard</h1>";
    html += "<h2>Current Data</h2>";
    html += "<p>Heart Rate: <span id='heartRate'>Loading...</span></p>";
    html += "<p>Gas Level: <span id='gasValue'>Loading...</span></p>";
    html += "<p>Status: <span id='status'>Checking...</span></p>";

    html += "<h2>Historical Data</h2>";
    html += "<canvas id='gasChart'></canvas>";
    html += "<canvas id='heartRateChart'></canvas>";

    html += "</div></body></html>";

    server.send(200, "text/html", html);
}

// Function to send sensor data including history
void handleSensorData() {
    int gasValue = analogRead(gasSensorPin);
    int heartRate = analogRead(heartRatePin);

    // Store in history
    gasHistory[historyIndex] = gasValue;
    heartRateHistory[historyIndex] = heartRate;
    historyIndex = (historyIndex + 1) % historySize;  // Loop back after 10 readings

    String status = "Safe";
    String statusColor = "green";

    if (gasValue > GAS_THRESHOLD || heartRate > HEART_RATE_THRESHOLD) {
        status = "DANGER! 🚨";
        statusColor = "red";
    }

    String json = "{";
    json += "\"current\":{";
    json += "\"heartRate\":" + String(heartRate) + ",";
    json += "\"gas\":" + String(gasValue) + ",";
    json += "\"status\":\"" + status + "\",";
    json += "\"statusColor\":\"" + statusColor + "\"";
    json += "},";

    json += "\"history\":[";
    for (int i = 0; i < historySize; i++) {
        int index = (historyIndex + i) % historySize;
        json += "{";
        json += "\"heartRate\":" + String(heartRateHistory[index]) + ",";
        json += "\"gas\":" + String(gasHistory[index]);
        json += "}";
        if (i < historySize - 1) json += ",";
    }
    json += "]}";

    server.send(200, "application/json", json);
}

void setup() {
    Serial.begin(115200);
    pinMode(gasSensorPin, INPUT);
    pinMode(heartRatePin, INPUT);

    WiFi.begin(ssid, password);
    Serial.println("Connecting to WiFi...");
    
    while (WiFi.status() != WL_CONNECTED) {
        delay(500);
        Serial.print(".");
    }

    Serial.println("\nConnected to WiFi!");
    Serial.print("ESP32 IP Address: ");
    Serial.println(WiFi.localIP());

    server.on("/", handleRoot);
    server.on("/sensor", handleSensorData);
    server.begin();
    Serial.println("Web server started!");
}

void loop() {
    server.handleClient();
}