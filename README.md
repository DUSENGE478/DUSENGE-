from flask import Flask, request, jsonify, render_template
import requests

app = Flask(__name__)

# Route ya Home Page
@app.route('/')
def home():
    return render_template('index.html')  # Ukoresha template yo kwishyura

# Route yo kwishyura
@app.route('/pay', methods=['POST'])
def pay():
    # Fungura amakuru y'ishyura
    data = request.json
    phone_number = data.get('phone_number')
    amount = data.get('amount')

    if not phone_number or not amount:
        return jsonify({'status': 'error', 'message': 'Inzira yishyura siyo.'}), 400

    # Fata urugero rwo gukoresha API yishyura
    payment_response = initiate_payment(phone_number, amount)
    if payment_response.get('status') == 'success':
        return jsonify({'status': 'success', 'message': 'Kwishyura byagenze neza.'})
    else:
        return jsonify({'status': 'error', 'message': 'Kwishyura byanze.'}), 500

# Function yo gutangiza kwishyura (Urugero rwa API ya Mobile Money)
def initiate_payment(phone_number, amount):
    # API ya Mobile Money (Irimo urugero rwa MTN)
    api_url = "https://api.mtn.com/v1/payments"
    headers = {
        "Authorization": "Bearer YOUR_ACCESS_TOKEN",
        "Content-Type": "application/json",
    }
    payload = {
        "phone_number": phone_number,
        "amount": amount,
        "currency": "RWF",
        "description": "Payment for services",
    }
    try:
        response = requests.post(api_url, json=payload, headers=headers)
        return response.json()
    except Exception as e:
        return {"status": "error", "message": str(e)}

if __name__ == '__main__':
    app.run(debug=True)
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Kwishyura</title>
</head>
<body>
    <h1>Form yo Kwishyura</h1>
    <form id="payment-form">
        <label for="phone_number">Nomero ya Telefone:</label>
        <input type="text" id="phone_number" name="phone_number" required>
        <br>
        <label for="amount">Amafaranga:</label>
        <input type="number" id="amount" name="amount" required>
        <br>
        <button type="button" onclick="submitPayment()">Ohereza</button>
    </form>
    <script>
        async function submitPayment() {
            const phone_number = document.getElementById("phone_number").value;
            const amount = document.getElementById("amount").value;

            const response = await fetch('/pay', {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({ phone_number, amount })
            });

            const result = await response.json();
            alert(result.message);
        }
    </script>
</body>
</html>
pip install flask requests
python app.py
(payment done)
