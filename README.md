# Real-Time-Blockchain-Transaction-Time-Checker
Debugging tool to help developers solve problems on their projects. more usefull for bridges and dexe's.  still needs work but this should be a basic but yet functional project!!


Python libraries: requests, web3, flask (for web interface), pandas (for data handling)
  install by using this command: pip install requests web3 flask pandas

Use the web3 library to connect to the Base network: 

from web3 import Web3

# Connect to the Base network
infura_url = "YOUR_INFURA_PROJECT_URL"
web3 = Web3(Web3.HTTPProvider(infura_url))

if web3.isConnected():
    print("Connected to Base network")
else:
    print("Failed to connect to Base network")

# Fetching Real-Time Data
  # Get Latest Block: 
    latest_block = web3.eth.get_block('latest')
    print(latest_block)
    
  # Get Transactions from Block:
    block_number = latest_block['number']
    block = web3.eth.get_block(block_number, full_transactions=True)
    transactions = block['transactions']
  
  # Transaction Time Analysis:
    import datetime

    current_time = datetime.datetime.utcnow()
    block_time = datetime.datetime.utcfromtimestamp(block['timestamp'])
    transaction_time = current_time - block_time

    print(f"Transaction time: {transaction_time}")
    
 # Data Storage and Processing
   # Storing Transactions in a DataFrame:
     import pandas as pd

    tx_data = []
    for tx in transactions:
      tx_info = {
        'hash': tx['hash'].hex(),
        'from': tx['from'],
        'to': tx['to'],
        'value': tx['value'],
        'gas': tx['gas'],
        'gasPrice': tx['gasPrice'],
        'time': block_time
      }
      tx_data.append(tx_info)

    df = pd.DataFrame(tx_data)
    print(df.head())

    
  # Analyzing Transaction Times:
    df['transaction_time'] = current_time - df['time']
    print(df[['hash', 'transaction_time']])

 # User Interface
    def main():
    while True:
        choice = input("Enter '1' to check latest transaction times, 'q' to quit: ")
        if choice == '1':
            latest_block = web3.eth.get_block('latest')
            block_number = latest_block['number']
            block = web3.eth.get_block(block_number, full_transactions=True)
            transactions = block['transactions']
            
            current_time = datetime.datetime.utcnow()
            block_time = datetime.datetime.utcfromtimestamp(block['timestamp'])
            transaction_times = []

            for tx in transactions:
                tx_time = current_time - block_time
                transaction_times.append((tx['hash'].hex(), tx_time))

            for tx_hash, tx_time in transaction_times:
                print(f"Transaction {tx_hash} took {tx_time}")

        elif choice == 'q':
            break
        else:
            print("Invalid choice, please try again.")

    if __name__ == "__main__":
        main()

# Web Interface (using Flask):
  
      from flask import Flask, jsonify
    
    app = Flask(__name__)
    
    @app.route('/transactions', methods=['GET'])
    def get_transactions():
        latest_block = web3.eth.get_block('latest')
        block_number = latest_block['number']
        block = web3.eth.get_block(block_number, full_transactions=True)
        transactions = block['transactions']
    
        current_time = datetime.datetime.utcnow()
        block_time = datetime.datetime.utcfromtimestamp(block['timestamp'])
        transaction_times = []
    
        for tx in transactions:
            tx_time = current_time - block_time
            transaction_times.append({
                'hash': tx['hash'].hex(),
                'transaction_time': str(tx_time)
            })
    
        return jsonify(transaction_times)
    
    if __name__ == "__main__":
        app.run(debug=True)






  Deployment
  
Deploy the Flask app to a web server or cloud service (e.g., Heroku, AWS).
Ensure the environment variables (e.g., Infura URL) are properly set in the deployment environment.
