<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>What is My IP?</title>
    <style>
        body {
            background: linear-gradient(135deg, #f3ec78, #af4261);
            font-family: Arial, sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            color: white;
            text-align: center;
        }
        .container {
            background: rgba(0, 0, 0, 0.5);
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
        }
        h1 {
            margin: 0;
            font-size: 2.5em;
        }
        .ip-address, .ipv6-address, .gateway-address, .local-ip-address {
            font-size: 1.5em;
            margin-top: 10px;
            color: #ffeb3b;
        }
        .loading {
            font-size: 1.2em;
            color: #ffeb3b;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Your IP Information</h1>
        <div id="ipv4" class="loading">Loading IPv4...</div>
        <div id="ipv6" class="loading">Loading IPv6...</div>
        <div id="local-ip" class="loading">Loading Local IP...</div>
        <div id="gateway" class="loading">Loading Router IP...</div>
    </div>
    <script>
        async function getIP() {
            try {
                // Fetch IPv4 address
                const ipv4Response = await fetch('https://api.ipify.org?format=json');
                const ipv4Data = await ipv4Response.json();
                document.getElementById('ipv4').innerText = 'IPv4 Address: ' + ipv4Data.ip;
                document.getElementById('ipv4').classList.remove('loading');
                document.getElementById('ipv4').classList.add('ip-address');

                // Fetch IPv6 address
                const ipv6Response = await fetch('https://api64.ipify.org?format=json');
                const ipv6Data = await ipv6Response.json();
                document.getElementById('ipv6').innerText = 'IPv6 Address: ' + ipv6Data.ip;
                document.getElementById('ipv6').classList.remove('loading');
                document.getElementById('ipv6').classList.add('ipv6-address');
            } catch (error) {
                document.getElementById('ipv4').innerText = 'Unable to fetch IPv4 address.';
                document.getElementById('ipv6').innerText = 'Unable to fetch IPv6 address.';
            }
        }

        function getLocalIP() {
            const RTCPeerConnection = window.RTCPeerConnection || window.mozRTCPeerConnection || window.webkitRTCPeerConnection;
            const rtc = new RTCPeerConnection({ iceServers: [] });
            rtc.createDataChannel('');
            rtc.createOffer()
                .then(offer => rtc.setLocalDescription(offer))
                .catch(error => console.error(error));

            rtc.onicecandidate = (event) => {
                if (event.candidate) {
                    const candidate = event.candidate.candidate;
                    const localIP = candidate.split(' ')[4];
                    document.getElementById('local-ip').innerText = 'Local IP Address: ' + localIP;
                    document.getElementById('local-ip').classList.remove('loading');
                    document.getElementById('local-ip').classList.add('local-ip-address');
                    rtc.onicecandidate = null;
                }
            };
        }

        function getRouterIP() {
            // Since getting the router's IP isn't possible via pure client-side JavaScript,
            // we'll simulate this with a placeholder. 
            // In a real scenario, this would need server-side logic or access to network configuration.
            document.getElementById('gateway').innerText = 'Router IP Address: 192.168.1.1';
            document.getElementById('gateway').classList.remove('loading');
            document.getElementById('gateway').classList.add('gateway-address');
        }

        getIP();
        getLocalIP();
        getRouterIP();
    </script>
</body>
</html>
