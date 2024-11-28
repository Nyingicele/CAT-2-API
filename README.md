from flask import Flask, jsonify, request

app = Flask(_name_)

# In-memory data storage
products = []
product_id_counter = 1

# Route to get all products
@app.route('/products', methods=['GET'])
def get_products():
    return jsonify(products), 200

# Route to get a single product by ID
@app.route('/products/<int:product_id>', methods=['GET'])
def get_product(product_id):
    for product in products:
        if product['id'] == product_id:
            return jsonify(product), 200
    return jsonify({'error': 'Product not found'}), 404

# Route to create a new product
@app.route('/products', methods=['POST'])
def create_product():
    global product_id_counter
    data = request.json
    if not data or not all(key in data for key in ['name', 'description', 'price']):
        return jsonify({'error': 'Invalid input'}), 400

    product = {
        'id': product_id_counter,
        'name': data['name'],
        'description': data['description'],
        'price': float(data['price']),
    }
    products.append(product)
    product_id_counter += 1
    return jsonify(product), 201

# Route to update a product
@app.route('/products/<int:product_id>', methods=['PUT'])
def update_product(product_id):
    data = request.json
    for product in products:
        if product['id'] == product_id:
            product.update({
                'name': data.get('name', product['name']),
                'description': data.get('description', product['description']),
                'price': float(data.get('price', product['price']))
            })
            return jsonify(product), 200
    return jsonify({'error': 'Product not found'}), 404

# Route to delete a product
@app.route('/products/<int:product_id>', methods=['DELETE'])
def delete_product(product_id):
    global products
    products = [product for product in products if product['id'] != product_id]
    return jsonify({'message': 'Product deleted'}), 200

if _name_ == '_main_':
    app.run(debug=True)
