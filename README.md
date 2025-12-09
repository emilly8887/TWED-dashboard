<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TWED - Threat Weapon Early Detection</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        body { margin: 0; overflow-x: hidden; background: #0f172a; padding-bottom: 80px; }
        .camera-box { transition: all 0.3s ease; }
        .camera-box:hover { transform: scale(1.02); }
        .alert-pulse { animation: pulse 2s infinite; }
        @keyframes pulse { 0%, 100% { opacity: 1; } 50% { opacity: 0.7; } }
        .live-indicator { animation: blink 1s infinite; }
        @keyframes blink { 0%, 50% { opacity: 1; } 51%, 100% { opacity: 0.3; } }
        video { background: #000; }
        .camera-feed { width: 100%; height: 120px; object-fit: cover; }
    </style>
</head>
<body class="bg-gray-900 text-white">
    
    <!-- Header -->
    <div class="flex items-center justify-between mb-6 bg-gray-800 p-4 shadow-lg">
        <div class="flex items-center gap-3">
            <div class="w-12 h-12 bg-red-600 rounded-full flex items-center justify-center">
                <svg class="w-7 h-7" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z"/>
                </svg>
            </div>
            <div>
                <h1 class="text-3xl font-bold">TWED</h1>
                <p class="text-xs text-gray-400">Threat Weapon Early Detection</p>
                <span class="text-green-400 text-sm bg-green-900 px-3 py-1 rounded-full font-semibold">SYSTEM ARMED</span>
            </div>
        </div>
        <div class="flex items-center gap-8 text-base">
            <div><span class="text-gray-400">Alerts: </span><span class="text-red-400 font-bold text-xl" id="alertCount">5 Active</span></div>
            <div><span class="text-gray-400">Cameras: </span><span class="text-green-400 font-bold text-xl">24/24</span></div>
            <div class="text-gray-300 font-mono text-lg" id="currentTime"></div>
        </div>
    </div>

    <div class="px-4" id="dashboardView">
        
        <!-- Threat Level -->
        <div class="bg-gradient-to-r border-red-500 from-red-900 to-gray-900 rounded-xl p-4 shadow-lg mb-4 border-2">
            <div class="flex items-center justify-between">
                <div class="flex items-center gap-3">
                    <div class="w-12 h-12 rounded-full flex items-center justify-center">
                        <svg class="w-8 h-8" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01"/>
                        </svg>
                    </div>
                    <div>
                        <h3 class="text-2xl font-bold text-red-400" id="threatLevelText">CRITICAL THREAT LEVEL</h3>
                        <p class="text-sm text-gray-400" id="threatDescription">5 active threats detected - Emergency response required!</p>
                    </div>
                </div>
                <div class="text-right">
                    <div class="text-4xl font-bold" id="threatScore">100</div>
                    <div class="text-xs text-gray-400">Threat Score</div>
                </div>
            </div>
            <div class="mt-3 bg-gray-700 rounded-full h-3 overflow-hidden">
                <div class="h-full bg-gradient-to-r from-red-400 to-red-600" id="threatBar" style="width: 100%;"></div>
            </div>
        </div>

        <!-- Video Upload Section -->
        <div class="bg-gray-800 rounded-xl p-5 shadow-lg mb-4">
            <div class="flex items-center justify-between mb-4">
                <div class="flex items-center gap-3">
                    <svg class="w-7 h-7 text-blue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 10l4.553-2.276A1 1 0 0121 8.618v6.764a1 1 0 01-1.447.894L15 14M5 18h8a2 2 0 002-2V8a2 2 0 00-2-2H5a2 2 0 00-2 2v8a2 2 0 002 2z"/>
                    </svg>
                    <h2 class="text-2xl font-bold">Live Feed Analysis</h2>
                </div>
                <div class="flex gap-2">
                    <input type="file" id="videoUpload" accept="video/*" class="hidden">
                    <button onclick="document.getElementById('videoUpload').click()" class="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-lg font-semibold">
                        Load Video Feed
                    </button>
                    <button onclick="simulateDetection()" class="bg-red-600 hover:bg-red-700 text-white px-4 py-2 rounded-lg font-semibold">
                        Simulate Detection
                    </button>
                </div>
            </div>

            <div class="bg-black rounded-lg overflow-hidden relative" style="height: 400px;">
                <video id="videoPlayer" class="w-full h-full object-contain" controls></video>
                <div id="noVideoMsg" class="absolute inset-0 flex items-center justify-center text-gray-500">
                    <div class="text-center">
                        <svg class="w-20 h-20 mx-auto mb-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 10l4.553-2.276A1 1 0 0121 8.618v6.764a1 1 0 01-1.447.894L15 14M5 18h8a2 2 0 002-2V8a2 2 0 00-2-2H5a2 2 0 00-2 2v8a2 2 0 002 2z"/>
                        </svg>
                        <p class="text-xl">No video loaded</p>
                        <p class="text-sm">Click "Load Video Feed" to analyze</p>
                    </div>
                </div>
            </div>
            
            <div class="mt-3 bg-red-900 border-2 border-red-500 rounded-lg p-4">
                <div class="flex items-center gap-3">
                    <svg class="w-8 h-8 text-red-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z"/>
                    </svg>
                    <div>
                        <div class="text-xl font-bold text-red-400">⚠️ THREAT DETECTED!</div>
                        <div class="text-lg text-white mt-1">Weapon Type: <span class="font-bold text-red-300">Handgun</span></div>
                        <div class="text-sm text-gray-300">Location: Cam 07 - Lobby Area | Confidence: 84%</div>
                    </div>
                </div>
            </div>

            <div class="mt-3 grid grid-cols-3 gap-3">
                <div class="bg-gray-900 p-3 rounded-lg text-center border border-red-500">
                    <div class="text-2xl font-bold text-red-400" id="weaponsDetected">3</div>
                    <div class="text-xs text-gray-400">Weapons Detected</div>
                </div>
                <div class="bg-gray-900 p-3 rounded-lg text-center border border-yellow-500">
                    <div class="text-2xl font-bold text-yellow-400" id="suspectsTracked">4</div>
                    <div class="text-xs text-gray-400">Suspects Tracked</div>
                </div>
                <div class="bg-gray-900 p-3 rounded-lg text-center border border-blue-500">
                    <div class="text-2xl font-bold text-blue-400">86%</div>
                    <div class="text-xs text-gray-400">Analysis Accuracy</div>
                </div>
            </div>
        </div>

        <div class="grid grid-cols-3 gap-6">
            <div class="col-span-2">
                <!-- Camera Grid -->
                <div class="grid grid-cols-4 gap-4 mb-6" id="cameraGrid"></div>

                <!-- Video Storage -->
                <div class="bg-gray-800 p-6 rounded-xl shadow-lg mb-4">
                    <div class="flex items-center justify-between mb-4">
                        <h3 class="text-xl font-bold text-green-400">📁 Video Storage</h3>
                        <div class="flex gap-2">
                            <input type="file" id="folderUpload" multiple accept="video/*" class="hidden">
                            <button onclick="document.getElementById('folderUpload').click()" class="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded font-semibold">+ Add Videos</button>
                            <button onclick="clearStorage()" class="bg-red-600 hover:bg-red-700 text-white px-4 py-2 rounded font-semibold">Clear All</button>
                        </div>
                    </div>
                    <div class="grid grid-cols-2 gap-4 mb-4">
                        <div class="text-center p-4 bg-gray-900 rounded-lg">
                            <div class="text-3xl font-bold text-green-400" id="videoCount">0</div>
                            <div class="text-sm text-gray-400">Videos Stored</div>
                        </div>
                        <div class="text-center p-4 bg-gray-900 rounded-lg">
                            <div class="text-3xl font-bold text-blue-400" id="storageSize">0 MB</div>
                            <div class="text-sm text-gray-400">Storage Used</div>
                        </div>
                    </div>
                    
                    <div class="bg-gray-900 rounded-lg p-4 mb-4 max-h-64 overflow-y-auto">
                        <h4 class="text-sm font-bold text-gray-400 mb-3">Stored Videos</h4>
                        <div id="videoLibraryGrid" class="space-y-2">
                            <div class="text-center text-gray-500 text-sm py-8">No videos stored yet.</div>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Alerts Panel -->
            <div class="bg-gray-800 rounded-xl p-5 shadow-lg">
                <div class="flex items-center gap-3 mb-5 border-b border-gray-700 pb-3">
                    <svg class="w-7 h-7 text-red-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01"/>
                    </svg>
                    <h2 class="text-2xl font-bold">Active Alerts (<span id="alertCountSidebar">5</span>)</h2>
                </div>
                <div id="alertsContainer" class="space-y-4 max-h-96 overflow-y-auto"></div>
            </div>
        </div>
    </div>

    <!-- Analytics View -->
    <div class="px-4" id="analyticsView" style="display: none;">
        <div class="bg-gray-800 rounded-xl p-6 shadow-lg mb-6">
            <h2 class="text-3xl font-bold mb-6">📊 Analytics Dashboard</h2>
            <div class="grid grid-cols-4 gap-4 mb-6">
                <div class="bg-gray-900 p-6 rounded-lg text-center border-2 border-red-500">
                    <div class="text-4xl font-bold text-red-400">27</div>
                    <div class="text-sm text-gray-400">Total Detections</div>
                    <div class="text-xs text-green-400 mt-2">↑ 15% from last week</div>
                </div>
                <div class="bg-gray-900 p-6 rounded-lg text-center border-2 border-yellow-500">
                    <div class="text-4xl font-bold text-yellow-400">5</div>
                    <div class="text-sm text-gray-400">Active Threats</div>
                    <div class="text-xs text-red-400 mt-2">↑ 2 new today</div>
                </div>
                <div class="bg-gray-900 p-6 rounded-lg text-center border-2 border-green-500">
                    <div class="text-4xl font-bold text-green-400">86%</div>
                    <div class="text-sm text-gray-400">Detection Rate</div>
                    <div class="text-xs text-green-400 mt-2">↑ 3% improvement</div>
                </div>
                <div class="bg-gray-900 p-6 rounded-lg text-center border-2 border-blue-500">
                    <div class="text-4xl font-bold text-blue-400">98%</div>
                    <div class="text-sm text-gray-400">System Uptime</div>
                    <div class="text-xs text-green-400 mt-2">Last 30 days</div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-6 mb-6">
                <div class="bg-gray-900 p-6 rounded-lg">
                    <h3 class="text-xl font-bold mb-4 text-blue-400">Detection Timeline (Last 24h)</h3>
                    <div class="space-y-3">
                        <div class="flex items-center gap-3">
                            <div class="text-gray-400 text-sm w-20">00:00</div>
                            <div class="flex-1 bg-gray-700 rounded-full h-6 overflow-hidden">
                                <div class="bg-red-500 h-full" style="width: 20%"></div>
                            </div>
                            <div class="text-white font-bold w-12">2</div>
                        </div>
                        <div class="flex items-center gap-3">
                            <div class="text-gray-400 text-sm w-20">06:00</div>
                            <div class="flex-1 bg-gray-700 rounded-full h-6 overflow-hidden">
                                <div class="bg-yellow-500 h-full" style="width: 10%"></div>
                            </div>
                            <div class="text-white font-bold w-12">1</div>
                        </div>
                        <div class="flex items-center gap-3">
                            <div class="text-gray-400 text-sm w-20">12:00</div>
                            <div class="flex-1 bg-gray-700 rounded-full h-6 overflow-hidden">
                                <div class="bg-red-500 h-full" style="width: 60%"></div>
                            </div>
                            <div class="text-white font-bold w-12">6</div>
                        </div>
                        <div class="flex items-center gap-3">
                            <div class="text-gray-400 text-sm w-20">18:00</div>
                            <div class="flex-1 bg-gray-700 rounded-full h-6 overflow-hidden">
                                <div class="bg-orange-500 h-full" style="width: 40%"></div>
                            </div>
                            <div class="text-white font-bold w-12">4</div>
                        </div>
                    </div>
                </div>

                <div class="bg-gray-900 p-6 rounded-lg">
                    <h3 class="text-xl font-bold mb-4 text-red-400">Weapon Type Distribution</h3>
                    <div class="space-y-4">
                        <div>
                            <div class="flex justify-between mb-1">
                                <span class="text-sm text-gray-400">Handguns</span>
                                <span class="text-white font-bold">45%</span>
                            </div>
                            <div class="bg-gray-700 rounded-full h-4 overflow-hidden">
                                <div class="bg-red-500 h-full" style="width: 45%"></div>
                            </div>
                        </div>
                        <div>
                            <div class="flex justify-between mb-1">
                                <span class="text-sm text-gray-400">Knives</span>
                                <span class="text-white font-bold">35%</span>
                            </div>
                            <div class="bg-gray-700 rounded-full h-4 overflow-hidden">
                                <div class="bg-orange-500 h-full" style="width: 35%"></div>
                            </div>
                        </div>
                        <div>
                            <div class="flex justify-between mb-1">
                                <span class="text-sm text-gray-400">Rifles</span>
                                <span class="text-white font-bold">20%</span>
                            </div>
                            <div class="bg-gray-700 rounded-full h-4 overflow-hidden">
                                <div class="bg-yellow-500 h-full" style="width: 20%"></div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <div class="grid grid-cols-3 gap-6">
                <div class="bg-gray-900 p-6 rounded-lg">
                    <h3 class="text-lg font-bold mb-4 text-purple-400">Top Alert Locations</h3>
                    <div class="space-y-3">
                        <div class="flex justify-between items-center">
                            <span class="text-gray-400">Lobby Area</span>
                            <span class="bg-red-600 px-3 py-1 rounded-full text-sm font-bold">12</span>
                        </div>
                        <div class="flex justify-between items-center">
                            <span class="text-gray-400">Main Entrance</span>
                            <span class="bg-orange-600 px-3 py-1 rounded-full text-sm font-bold">8</span>
                        </div>
                        <div class="flex justify-between items-center">
                            <span class="text-gray-400">Parking Lot</span>
                            <span class="bg-yellow-600 px-3 py-1 rounded-full text-sm font-bold">5</span>
                        </div>
                        <div class="flex justify-between items-center">
                            <span class="text-gray-400">Gate A</span>
                            <span class="bg-green-600 px-3 py-1 rounded-full text-sm font-bold">2</span>
                        </div>
                    </div>
                </div>

                <div class="bg-gray-900 p-6 rounded-lg">
                    <h3 class="text-lg font-bold mb-4 text-green-400">Response Times</h3>
                    <div class="space-y-3">
                        <div>
                            <div class="text-sm text-gray-400 mb-1">Avg Response</div>
                            <div class="text-2xl font-bold text-green-400">2.3s</div>
                        </div>
                        <div>
                            <div class="text-sm text-gray-400 mb-1">Fastest</div>
                            <div class="text-xl font-bold text-blue-400">0.8s</div>
                        </div>
                        <div>
                            <div class="text-sm text-gray-400 mb-1">Slowest</div>
                            <div class="text-xl font-bold text-orange-400">5.2s</div>
                        </div>
                    </div>
                </div>

                <div class="bg-gray-900 p-6 rounded-lg">
                    <h3 class="text-lg font-bold mb-4 text-yellow-400">False Positive Rate</h3>
                    <div class="text-center py-4">
                        <div class="text-5xl font-bold text-green-400">3.2%</div>
                        <div class="text-sm text-gray-400 mt-2">Industry avg: 8.5%</div>
                        <div class="text-xs text-green-400 mt-3">↓ 62% better than average</div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- Settings View -->
    <div class="px-4" id="settingsView" style="display: none;">
        <div class="bg-gray-800 rounded-xl p-6 shadow-lg">
            <h2 class="text-3xl font-bold mb-6">⚙️ System Settings</h2>
            <div class="grid grid-cols-2 gap-6">
                <div class="bg-gray-900 p-6 rounded-lg">
                    <h3 class="text-xl font-bold mb-4 text-blue-400">Detection Settings</h3>
                    <div class="space-y-4">
                        <div>
                            <label class="text-sm text-gray-400 block mb-2">Confidence Threshold</label>
                            <input type="range" min="50" max="95" value="75" class="w-full" oninput="document.getElementById('confValue').textContent = this.value + '%'">
                            <div class="text-xs text-gray-500 mt-1">Current: <span id="confValue">75%</span></div>
                        </div>
                        <div>
                            <label class="text-sm text-gray-400 block mb-2">Detection Sensitivity</label>
                            <select class="w-full bg-gray-800 text-white p-2 rounded border border-gray-700">
                                <option>Low - Fewer false alarms</option>
                                <option selected>Medium - Balanced</option>
                                <option>High - Maximum detection</option>
                            </select>
                        </div>
                        <div>
                            <label class="text-sm text-gray-400 block mb-2">Weapon Categories</label>
                            <div class="space-y-2">
                                <label class="flex items-center"><input type="checkbox" checked class="mr-2"><span class="text-white">Handguns</span></label>
                                <label class="flex items-center"><input type="checkbox" checked class="mr-2"><span class="text-white">Knives</span></label>
                                <label class="flex items-center"><input type="checkbox" checked class="mr-2"><span class="text-white">Rifles</span></label>
                                <label class="flex items-center"><input type="checkbox" class="mr-2"><span class="text-white">Explosives</span></label>
                            </div>
                        </div>
                        <div>
                            <label class="text-sm text-gray-400 block mb-2">AI Model Version</label>
                            <select class="w-full bg-gray-800 text-white p-2 rounded border border-gray-700">
                                <option>YOLOv7 - Legacy</option>
                                <option selected>YOLOv8 - Current</option>
                                <option>YOLOv9 - Beta</option>
                            </select>
                        </div>
                    </div>
                </div>
                <div class="bg-gray-900 p-6 rounded-lg">
                    <h3 class="text-xl font-bold mb-4 text-red-400">Alert Settings</h3>
                    <div class="space-y-4">
                        <div>
                            <label class="text-sm text-gray-400 block mb-2">Notification Email</label>
                            <input type="email" value="security@twed.com" class="w-full bg-gray-800 text-white p-2 rounded border border-gray-700">
                        </div>
                        <div>
                            <label class="text-sm text-gray-400 block mb-2">Emergency Contact</label>
                            <input type="tel" value="+256-700-123456" class="w-full bg-gray-800 text-white p-2 rounded border border-gray-700">
                        </div>
                        <div>
                            <label class="text-sm text-gray-400 block mb-2">Secondary Contact</label>
                            <input type="tel" placeholder="+256-XXX-XXXXXX" class="w-full bg-gray-800 text-white p-2 rounded border border-gray-700">
                        </div>
                        <div class="flex items-center justify-between">
                            <span class="text-sm text-gray-400">Sound Alerts</span>
                            <input type="checkbox" checked class="w-5 h-5">
                        </div>
                        <div class="flex items-center justify-between">
                            <span class="text-sm text-gray-400">Email Notifications</span>
                            <input type="checkbox" checked class="w-5 h-5">
                        </div>
                        <div class="flex items-center justify-between">
                            <span class="text-sm text-gray-400">SMS Alerts</span>
                            <input type="checkbox" class="w-5 h-5">
                        </div>
                        <div class="flex items-center justify-between">
                            <span class="text-sm text-gray-400">Push Notifications</span>
                            <input type="checkbox" checked class="w-5 h-5">
                        </div>
                        <div class="flex items-center justify-between">
                            <span class="text-sm text-gray-400">Auto-Alert Authorities</span>
                            <input type="checkbox" checked class="w-5 h-5">
                        </div>
                        <div>
                            <label class="text-sm text-gray-400 block mb-2">Alert Priority Level</label>
                            <select class="w-full bg-gray-800 text-white p-2 rounded border border-gray-700">
                                <option>Low - Log only</option>
                                <option>Medium - Notify team</option>
                                <option selected>High - Immediate action</option>
                                <option>Critical - Full lockdown</option>
                            </select>
                        </div>
                    </div>
                </div>
                <div class="bg-gray-900 p-6 rounded-lg">
                    <h3 class="text-xl font-bold mb-4 text-green-400">Camera Settings</h3>
                    <div class="space-y-4">
                        <div>
                            <label class="text-sm text-gray-400 block mb-2">Active Cameras</label>
                            <div class="text-2xl font-bold text-green-400">24 / 24 Online</div>
                        </div>
                        <div>
                            <label class="text-sm text-gray-400 block mb-2">Recording Quality</label>
                            <select class="w-full bg-gray-800 text-white p-2 rounded border border-gray-700">
                                <option>720p - Standard</option>
                                <option selected>1080p HD - Recommended</option>
                                <option>4K Ultra - High Quality</option>
                            </select>
                        </div>
                        <div>
                            <label class="text-sm text-gray-400 block mb-2">Frame Rate</label>
                            <select class="w-full bg-gray-800 text-white p-2 rounded border border-gray-700">
                                <option>15 FPS</option>
                                <option selected>30 FPS</option>
                                <option>60 FPS</option>
                            </select>
                        </div>
                        <div>
                            <label class="text-sm text-gray-400 block mb-2">Storage Duration</label>
                            <select class="w-full bg-gray-800 text-white p-2 rounded border border-gray-700">
                                <option>7 days</option>
                                <option selected>30 days</option>
                                <option>90 days</option>
                                <option>1 year</option>
                            </select>
                        </div>
                        <div>
                            <label class="text-sm text-gray-400 block mb-2">Night Vision Mode</label>
                            <select class="w-full bg-gray-800 text-white p-2 rounded border border-gray-700">
                                <option>Disabled</option>
                                <option selected>Auto - Based on lighting</option>
                                <option>Always On</option>
                            </select>
                        </div>
                        <div class="flex items-center justify-between">
                            <span class="text-sm text-gray-400">Motion Detection</span>
                            <input type="checkbox" checked class="w-5 h-5">
                        </div>
                        <button onclick="alert('✅ All 24 cameras restarted successfully!')" class="w-full bg-green-600 hover:bg-green-700 text-white py-2 rounded font-semibold">
                            Restart All Cameras
                        </button>
                        <button onclick="alert('📥 Camera settings exported successfully!')" class="w-full bg-blue-600 hover:bg-blue-700 text-white py-2 rounded font-semibold">
                            Export Camera Config
                        </button>
                    </div>
                </div>
                <div class="bg-gray-900 p-6 rounded-lg">
                    <h3 class="text-xl font-bold mb-4 text-purple-400">System Information</h3>
                    <div class="space-y-3 text-sm">
                        <div class="flex justify-between"><span class="text-gray-400">System Version</span><span class="text-white font-bold">TWED v3.2.0</span></div>
                        <div class="flex justify-between"><span class="text-gray-400">AI Model</span><span class="text-white font-bold">YOLOv8</span></div>
                        <div class="flex justify-between"><span class="text-gray-400">Last Update</span><span class="text-white">Dec 09, 2024</span></div>
                        <div class="flex justify-between"><span class="text-gray-400">System Uptime</span><span class="text-green-400 font-bold">98.2%</span></div>
                        <div class="flex justify-between"><span class="text-gray-400">Database Status</span><span class="text-green-400 font-bold">● Connected</span></div>
                        <div class="flex justify-between"><span class="text-gray-400">API Status</span><span class="text-green-400 font-bold">● Online</span></div>
                        <div class="flex justify-between"><span class="text-gray-400">Total Detections</span><span class="text-white font-bold">1,247</span></div>
                        <div class="flex justify-between"><span class="text-gray-400">Avg Response Time</span><span class="text-blue-400 font-bold">2.3s</span></div>
                        <div class="flex justify-between"><span class="text-gray-400">Storage Used</span><span class="text-yellow-400 font-bold">342 GB / 1 TB</span></div>
                        <div class="flex justify-between"><span class="text-gray-400">CPU Usage</span><span class="text-green-400 font-bold">45%</span></div>
                        <div class="flex justify-between"><span class="text-gray-400">Memory Usage</span><span class="text-green-400 font-bold">62%</span></div>
                        <button onclick="alert('📥 System logs exported successfully!')" class="w-full bg-purple-600 hover:bg-purple-700 text-white py-2 rounded mt-3 font-semibold">Export System Logs</button>
                        <button onclick="alert('🔄 System backup completed successfully!')" class="w-full bg-blue-600 hover:bg-blue-700 text-white py-2 rounded font-semibold">Backup Settings</button>
                        <button onclick="alert('🔄 System restarting in 10 seconds...')" class="w-full bg-red-600 hover:bg-red-700 text-white py-2 rounded font-semibold">Restart System</button>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- Bottom Navigation -->
    <div class="fixed bottom-0 left-0 right-0 bg-gray-800 border-t-2 border-gray-700 p-4 flex justify-around shadow-lg">
        <button onclick="switchView('dashboard')" id="dashboardBtn" class="flex items-center gap-2 text-white">
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6a2 2 0 012-2h2a2 2 0 012 2v2a2 2 0 01-2 2H6a2 2 0 01-2-2V6z"/>
            </svg>
            <span class="font-semibold">Dashboard</span>
        </button>
        <button onclick="switchView('analytics')" id="analyticsBtn" class="flex items-center gap-2 text-gray-400">
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6"/>
            </svg>
            <span class="font-semibold">Analytics</span>
        </button>
        <button onclick="switchView('settings')" id="settingsBtn" class="flex items-center gap-2 text-gray-400">
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10.325 4.317c.426-1.756 2.924-1.756 3.35 0"/>
            </svg>
            <span class="font-semibold">Settings</span>
        </button>
    </div>

    <script>
        let storedVideos = [];
        let totalStorageSize = 0;
        
        const cameras = [
            { id: 1, name: 'Cam 01', location: 'Main Entrance', alert: 'Knife', confidence: 92 },
            { id: 2, name: 'Cam 02', location: 'Lobby Area', alert: null, confidence: 0 },
            { id: 3, name: 'Cam 03', location: 'Gate A', alert: 'Handgun', confidence: 88 },
            { id: 4, name: 'Cam 04', location: 'Lobby Area', alert: 'Knife', confidence: 83 },
            { id: 5, name: 'Cam 05', location: 'Back Exit', alert: null, confidence: 0 },
            { id: 6, name: 'Cam 06', location: 'Parking Lot', alert: 'Rifle', confidence: 79 },
            { id: 7, name: 'Cam 07', location: 'Lobby Area', alert: 'Handgun', confidence: 84 },
            { id: 8, name: 'Cam 08', location: 'Side Gate', alert: null, confidence: 0 },
            { id: 9, name: 'Cam 09', location: 'Rooftop', alert: null, confidence: 0 },
            { id: 10, name: 'Cam 10', location: 'Server Room', alert: null, confidence: 0 },
            { id: 11, name: 'Cam 11', location: 'Cafeteria', alert: null, confidence: 0 },
            { id: 12, name: 'Cam 12', location: 'Stairwell A', alert: null, confidence: 0 },
            { id: 13, name: 'Cam 13', location: 'Parking B', alert: null, confidence: 0 },
            { id: 14, name: 'Cam 14', location: 'Loading Bay', alert: null, confidence: 0 },
            { id: 15, name: 'Cam 15', location: 'East Wing', alert: null, confidence: 0 },
            { id: 16, name: 'Cam 16', location: 'West Wing', alert: null, confidence: 0 },
            { id: 17, name: 'Cam 17', location: 'Reception', alert: null, confidence: 0 },
            { id: 18, name: 'Cam 18', location: 'Corridor 1', alert: null, confidence: 0 },
            { id: 19, name: 'Cam 19', location: 'Corridor 2', alert: null, confidence: 0 },
            { id: 20, name: 'Cam 20', location: 'Emergency Exit', alert: null, confidence: 0 },
            { id: 21, name: 'Cam 21', location: 'Storage', alert: null, confidence: 0 },
            { id: 22, name: 'Cam 22', location: 'Garage', alert: null, confidence: 0 },
            { id: 23, name: 'Cam 23', location: 'Basement', alert: null, confidence: 0 },
            { id: 24, name: 'Cam 24', location: 'Perimeter', alert: null, confidence: 0 }
        ];

        let alerts = [
            { id: 1, time: new Date().toLocaleTimeString(), weapon: 'Handgun', confidence: 84, camera: 'Cam 07', location: 'Lobby Area' },
            { id: 2, time: new Date(Date.now() - 15000).toLocaleTimeString(), weapon: 'Knife', confidence: 83, camera: 'Cam 04', location: 'Lobby Area' },
            { id: 3, time: new Date(Date.now() - 93000).toLocaleTimeString(), weapon: 'Knife', confidence: 92, camera: 'Cam 01', location: 'Main Entrance' },
            { id: 4, time: new Date(Date.now() - 215000).toLocaleTimeString(), weapon: 'Handgun', confidence: 88, camera: 'Cam 03', location: 'Gate A' },
            { id: 5, time: new Date(Date.now() - 338000).toLocaleTimeString(), weapon: 'Rifle', confidence: 79, camera: 'Cam 06', location: 'Parking Lot' }
        ];

        function updateTime() {
            const now = new Date();
            document.getElementById('currentTime').textContent = now.toLocaleTimeString('en-US');
        }
        setInterval(updateTime, 1000);
        updateTime();

        // Generate simulated camera feeds
        function createCameraFeed(cam) {
            const canvas = document.createElement('canvas');
            canvas.width = 320;
            canvas.height = 180;
            const ctx = canvas.getContext('2d');
            
            // Create gradient background
            const gradient = ctx.createLinearGradient(0, 0, 320, 180);
            gradient.addColorStop(0, '#1a1a2e');
            gradient.addColorStop(1, '#0f3460');
            ctx.fillStyle = gradient;
            ctx.fillRect(0, 0, 320, 180);
            
            // Add grid pattern
            ctx.strokeStyle = 'rgba(255, 255, 255, 0.1)';
            ctx.lineWidth = 1;
            for (let i = 0; i < 320; i += 40) {
                ctx.beginPath();
                ctx.moveTo(i, 0);
                ctx.lineTo(i, 180);
                ctx.stroke();
            }
            for (let i = 0; i < 180; i += 40) {
                ctx.beginPath();
                ctx.moveTo(0, i);
                ctx.lineTo(320, i);
                ctx.stroke();
            }
            
            // Add random "motion" rectangles
            for (let i = 0; i < 3; i++) {
                ctx.fillStyle = `rgba(${Math.random() * 100 + 155}, ${Math.random() * 100 + 155}, 255, 0.3)`;
                const x = Math.random() * 250;
                const y = Math.random() * 130;
                ctx.fillRect(x, y, 50 + Math.random() * 20, 30 + Math.random() * 20);
            }
            
            // Add alert box if weapon detected
            if (cam.alert) {
                ctx.strokeStyle = '#ef4444';
                ctx.lineWidth = 3;
                ctx.strokeRect(120, 60, 80, 60);
                ctx.fillStyle = '#ef4444';
                ctx.font = 'bold 12px Arial';
                ctx.fillText(cam.alert, 125, 75);
            }
            
            return canvas.toDataURL();
        }

        function renderCameras() {
            document.getElementById('cameraGrid').innerHTML = cameras.map(cam => `
                <div class="camera-box bg-gray-800 rounded-lg p-3 border-2 ${cam.alert ? 'border-red-500 alert-pulse' : 'border-gray-700'}">
                    ${cam.alert ? `<div class="bg-red-600 text-white px-2 py-1 rounded text-xs font-bold mb-2">⚠ ${cam.alert} ${cam.confidence}%</div>` : ''}
                    <div class="relative h-28 bg-gray-700 rounded mb-2 overflow-hidden">
                        <img src="${createCameraFeed(cam)}" class="camera-feed">
                        <div class="absolute top-1 left-1 text-xs text-green-400 font-mono flex items-center gap-1">
                            <span class="live-indicator">●</span> LIVE
                        </div>
                        <div class="absolute bottom-1 right-1 text-xs text-gray-300 font-mono bg-black bg-opacity-50 px-1 rounded">
                            ${new Date().toLocaleTimeString()}
                        </div>
                    </div>
                    <div class="flex justify-between items-center text-sm">
                        <span class="font-bold">${cam.name}</span>
                        <span class="text-xs text-gray-400">${cam.location}</span>
                    </div>
                </div>
            `).join('');
        }

        // Update camera feeds every 3 seconds
        setInterval(() => {
            renderCameras();
        }, 3000);

        function renderAlerts() {
            const container = document.getElementById('alertsContainer');
            if (alerts.length === 0) {
                container.innerHTML = '<div class="text-center text-gray-500 py-8">No active alerts</div>';
            } else {
                container.innerHTML = alerts.map(alert => `
                    <div class="bg-red-950 border border-red-600 rounded-lg p-4 alert-pulse">
                        <div class="text-xs text-gray-400 mb-1">${alert.time}</div>
                        <div class="text-lg font-bold mb-1">${alert.weapon} Detected (${alert.confidence}%)</div>
                        <div class="text-sm text-gray-300 mb-3">${alert.camera} → ${alert.location}</div>
                        <div class="flex gap-2">
                            <button onclick="acknowledgeAlert(${alert.id})" class="flex-1 bg-green-600 hover:bg-green-700 text-white py-2 px-3 rounded text-sm font-semibold">✓ Acknowledge</button>
                            <button onclick="dismissAlert(${alert.id})" class="flex-1 bg-gray-600 hover:bg-gray-700 text-white py-2 px-3 rounded text-sm font-semibold">✕ False</button>
                        </div>
                    </div>
                `).join('');
            }
            updateThreatLevel();
        }

        function acknowledgeAlert(id) {
            const alert = alerts.find(a => a.id === id);
            if (alert) {
                const cam = cameras.find(c => c.name === alert.camera);
                if (cam) {
                    cam.alert = null;
                    cam.confidence = 0;
                }
            }
            alerts = alerts.filter(a => a.id !== id);
            renderCameras();
            renderAlerts();
        }

        function dismissAlert(id) {
            const alert = alerts.find(a => a.id === id);
            if (alert) {
                const cam = cameras.find(c => c.name === alert.camera);
                if (cam) {
                    cam.alert = null;
                    cam.confidence = 0;
                }
            }
            alerts = alerts.filter(a => a.id !== id);
            renderCameras();
            renderAlerts();
        }

        function updateThreatLevel() {
            const count = alerts.length;
            document.getElementById('alertCount').textContent = `${count} Active`;
            document.getElementById('alertCountSidebar').textContent = count;
            
            let level, description, score, color;
            if (count === 0) {
                level = 'ALL CLEAR';
                description = 'No threats detected - System operating normally';
                score = 0;
                color = 'green';
            } else if (count === 1) {
                level = 'LOW THREAT LEVEL';
                description = `${count} threat detected - Monitoring situation`;
                score = 20;
                color = 'yellow';
            } else if (count === 2) {
                level = 'MODERATE THREAT LEVEL';
                description = `${count} threats detected - Increased vigilance required`;
                score = 40;
                color = 'orange';
            } else if (count === 3) {
                level = 'ELEVATED THREAT LEVEL';
                description = `${count} threats detected - Security teams alerted`;
                score = 60;
                color = 'orange';
            } else if (count === 4) {
                level = 'HIGH THREAT LEVEL';
                description = `${count} threats detected - Emergency protocols activated`;
                score = 80;
                color = 'red';
            } else {
                level = 'CRITICAL THREAT LEVEL';
                description = `${count} active threats detected - MAXIMUM ALERT!`;
                score = 100;
                color = 'red';
            }
            
            document.getElementById('threatLevelText').textContent = level;
            document.getElementById('threatLevelText').className = `text-2xl font-bold text-${color}-400`;
            document.getElementById('threatDescription').textContent = description;
            document.getElementById('threatScore').textContent = score;
            document.getElementById('threatBar').style.width = score + '%';
            
            // Change threat bar color based on level
            const threatBar = document.getElementById('threatBar');
            if (score === 0) {
                threatBar.className = 'h-full bg-gradient-to-r from-green-400 to-green-600';
            } else if (score <= 40) {
                threatBar.className = 'h-full bg-gradient-to-r from-yellow-400 to-yellow-600';
            } else if (score <= 60) {
                threatBar.className = 'h-full bg-gradient-to-r from-orange-400 to-orange-600';
            } else {
                threatBar.className = 'h-full bg-gradient-to-r from-red-400 to-red-600';
            }
            
            // Update stats
            const weaponCount = cameras.filter(c => c.alert).length;
            document.getElementById('weaponsDetected').textContent = weaponCount;
            document.getElementById('suspectsTracked').textContent = Math.min(weaponCount + 1, 10);
        }

        // Video upload handling
        document.getElementById('videoUpload').addEventListener('change', function(e) {
            const file = e.target.files[0];
            if (file) {
                const video = document.getElementById('videoPlayer');
                const url = URL.createObjectURL(file);
                video.src = url;
                document.getElementById('noVideoMsg').style.display = 'none';
                video.play();
            }
        });

        // Folder upload handling
        document.getElementById('folderUpload').addEventListener('change', function(e) {
            const files = Array.from(e.target.files);
            if (files.length > 0) {
                files.forEach(file => {
                    const videoData = {
                        id: Date.now() + Math.random(),
                        name: file.name,
                        size: file.size,
                        url: URL.createObjectURL(file),
                        date: new Date().toLocaleString()
                    };
                    storedVideos.push(videoData);
                    totalStorageSize += file.size;
                });
                updateVideoLibrary();
            }
        });

        function updateVideoLibrary() {
            document.getElementById('videoCount').textContent = storedVideos.length;
            const sizeMB = (totalStorageSize / (1024 * 1024)).toFixed(2);
            document.getElementById('storageSize').textContent = sizeMB + ' MB';
            
            const grid = document.getElementById('videoLibraryGrid');
            if (storedVideos.length === 0) {
                grid.innerHTML = '<div class="text-center text-gray-500 text-sm py-8">No videos stored yet.</div>';
            } else {
                grid.innerHTML = storedVideos.map(video => `
                    <div class="bg-gray-800 p-3 rounded-lg border border-gray-700">
                        <div class="bg-gray-900 rounded mb-2 h-20 flex items-center justify-center">
                            <svg class="w-8 h-8 text-blue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M14.752 11.168l-3.197-2.132A1 1 0 0010 9.87v4.263a1 1 0 001.555.832l3.197-2.132a1 1 0 000-1.664z"/>
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 12a9 9 0 11-18 0 9 9 0 0118 0z"/>
                            </svg>
                        </div>
                        <div class="text-xs font-bold text-white truncate mb-1" title="${video.name}">${video.name}</div>
                        <div class="text-xs text-gray-400">${(video.size / (1024 * 1024)).toFixed(2)} MB</div>
                        <div class="text-xs text-gray-500">${video.date}</div>
                        <div class="flex gap-1 mt-2">
                            <button onclick="playStoredVideo('${video.url}')" class="flex-1 bg-blue-600 hover:bg-blue-700 text-white py-1 px-2 rounded text-xs">Play</button>
                            <button onclick="deleteVideo(${video.id})" class="bg-red-600 hover:bg-red-700 text-white py-1 px-2 rounded text-xs">×</button>
                        </div>
                    </div>
                `).join('');
            }
        }

        function playStoredVideo(url) {
            const video = document.getElementById('videoPlayer');
            video.src = url;
            document.getElementById('noVideoMsg').style.display = 'none';
            video.play();
            window.scrollTo({ top: 0, behavior: 'smooth' });
        }

        function deleteVideo(id) {
            const video = storedVideos.find(v => v.id === id);
            if (video) {
                totalStorageSize -= video.size;
                storedVideos = storedVideos.filter(v => v.id !== id);
                updateVideoLibrary();
            }
        }

        function clearStorage() {
            if (confirm('Are you sure you want to delete all stored videos?')) {
                storedVideos = [];
                totalStorageSize = 0;
                updateVideoLibrary();
            }
        }

        // Alert sound
        function playAlertSound() {
            const audioContext = new (window.AudioContext || window.webkitAudioContext)();
            const oscillator = audioContext.createOscillator();
            const gainNode = audioContext.createGain();
            
            oscillator.connect(gainNode);
            gainNode.connect(audioContext.destination);
            
            oscillator.frequency.value = 800;
            oscillator.type = 'sine';
            
            gainNode.gain.setValueAtTime(0.3, audioContext.currentTime);
            gainNode.gain.exponentialRampToValueAtTime(0.01, audioContext.currentTime + 0.5);
            
            oscillator.start(audioContext.currentTime);
            oscillator.stop(audioContext.currentTime + 0.5);
            
            // Second beep
            setTimeout(() => {
                const oscillator2 = audioContext.createOscillator();
                const gainNode2 = audioContext.createGain();
                
                oscillator2.connect(gainNode2);
                gainNode2.connect(audioContext.destination);
                
                oscillator2.frequency.value = 1000;
                oscillator2.type = 'sine';
                
                gainNode2.gain.setValueAtTime(0.3, audioContext.currentTime);
                gainNode2.gain.exponentialRampToValueAtTime(0.01, audioContext.currentTime + 0.5);
                
                oscillator2.start(audioContext.currentTime);
                oscillator2.stop(audioContext.currentTime + 0.5);
            }, 600);
        }

        // Send email notification
        function sendEmailNotification(weapon, confidence, camera, location) {
            const notification = document.createElement('div');
            notification.className = 'fixed top-20 right-4 bg-green-600 text-white px-6 py-4 rounded-lg shadow-lg z-50 animate-pulse';
            notification.innerHTML = `
                <div class="flex items-center gap-3">
                    <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 8l7.89 5.26a2 2 0 002.22 0L21 8M5 19h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v10a2 2 0 002 2z"/>
                    </svg>
                    <div>
                        <div class="font-bold">📧 Email Alert Sent!</div>
                        <div class="text-sm">Authorities notified: ${weapon} at ${location}</div>
                    </div>
                </div>
            `;
            document.body.appendChild(notification);
            
            setTimeout(() => {
                notification.remove();
            }, 4000);
            
            console.log(`EMAIL SENT TO AUTHORITIES: ${weapon} detected at ${camera} - ${location} (${confidence}% confidence)`);
        }

        function simulateDetection() {
            const weapons = ['Handgun', 'Knife', 'Rifle', 'Explosive Device'];
            const randomCam = cameras[Math.floor(Math.random() * cameras.length)];
            const weapon = weapons[Math.floor(Math.random() * weapons.length)];
            const confidence = Math.floor(Math.random() * 20) + 75;
            
            // Add new alert
            alerts.unshift({
                id: Date.now(),
                time: new Date().toLocaleTimeString(),
                weapon: weapon,
                confidence: confidence,
                camera: randomCam.name,
                location: randomCam.location
            });
            
            // Update camera alert
            const cam = cameras.find(c => c.id === randomCam.id);
            cam.alert = weapon;
            cam.confidence = confidence;
            
            // Play alert sound
            playAlertSound();
            
            // Send email notification
            sendEmailNotification(weapon, confidence, randomCam.name, randomCam.location);
            
            // Show visual alert
            const alertBanner = document.createElement('div');
            alertBanner.className = 'fixed top-4 left-1/2 transform -translate-x-1/2 bg-red-600 text-white px-8 py-4 rounded-lg shadow-2xl z-50 text-xl font-bold animate-pulse';
            alertBanner.innerHTML = `🚨 THREAT DETECTED: ${weapon} at ${randomCam.location}! 🚨`;
            document.body.appendChild(alertBanner);
            
            setTimeout(() => {
                alertBanner.remove();
            }, 5000);
            
            renderCameras();
            renderAlerts();
        }

        function switchView(view) {
            document.getElementById('dashboardView').style.display = view === 'dashboard' ? 'block' : 'none';
            document.getElementById('analyticsView').style.display = view === 'analytics' ? 'block' : 'none';
            document.getElementById('settingsView').style.display = view === 'settings' ? 'block' : 'none';
            
            ['dashboardBtn', 'analyticsBtn', 'settingsBtn'].forEach(id => {
                const btn = document.getElementById(id);
                const isActive = id === view + 'Btn';
                btn.classList.toggle('text-white', isActive);
                btn.classList.toggle('text-gray-400', !isActive);
            });
        }

        // Initialize
        renderCameras();
        renderAlerts();
        updateVideoLibrary();
    </script>
</body>
</html>
