const express = require('express');
const bodyParser = require('body-parser');
const fs = require('fs');
const ProductManager = require('./ProductManager');
const CartManager = require('./CartManager');

const app = express();
const port = 8080;

app.use(bodyParser.json());

const productManager = new ProductManager('products.json');
const cartManager = new CartManager('carts.json', productManager);


const productsRouter = express.Router();

productsRouter.get('/', (req, res) => {
  const limit = req.query.limit ? parseInt(req.query.limit) : undefined;
  const allProducts = productManager.getProducts();
  const limitedProducts = limit ? allProducts.slice(0, limit) : allProducts;
  res.json(limitedProducts);
});

productsRouter.get('/:pid', (req, res) => {
  const productId = req.params.pid;
  try {
    const product = productManager.getProductById(productId);
    res.json(product);
  } catch (error) {
    res.status(404).json({ error: error.message });
  }
});

productsRouter.post('/', (req, res) => {
  const { title, description, code, price, stock, category, thumbnails } = req.body;

  if (!title || !description || !code || !price || !stock || !category || !thumbnails) {
    return res.status(400).json({ error: 'Todos los campos son obligatorios, excepto thumbnails.' });
  }

  const newProduct = productManager.addProduct({
    title,
    description,
    code,
    price,
    stock,
    category,
    thumbnails,
  });

  res.status(201).json(newProduct);
});

productsRouter.put('/:pid', (req, res) => {
  const productId = req.params.pid;
  const updatedFields = req.body;

  try {
    productManager.updateProduct(productId, updatedFields);
    res.json({ message: 'Producto actualizado exitosamente.' });
  } catch (error) {
    res.status(404).json({ error: error.message });
  }
});

productsRouter.delete('/:pid', (req, res) => {
  const productId = req.params.pid;

  try {
    productManager.deleteProduct(productId);
    res.json({ message: 'Producto eliminado exitosamente.' });
  } catch (error) {
    res.status(404).json({ error: error.message });
  }
});

app.use('/api/products', productsRouter);

// Rutas para carritos
const cartsRouter = express.Router();

cartsRouter.post('/', (req, res) => {
  const newCart = cartManager.createCart();
  res.status(201).json(newCart);
});

cartsRouter.get('/:cid', (req, res) => {
  const cartId = req.params.cid;
  try {
    const cart = cartManager.getCartById(cartId);
    res.json(cart);
  } catch (error) {
    res.status(404).json({ error: error.message });
  }
});

cartsRouter.post('/:cid/product/:pid', (req, res) => {
  const cartId = req.params.cid;
  const productId = req.params.pid;
  const { quantity } = req.body;

  try {
    const updatedCart = cartManager.addProductToCart(cartId, productId, quantity);
    res.json(updatedCart);
  } catch (error) {
    res.status(400).json({ error: error.message });
  }
});

app.use('/api/carts', cartsRouter);

// Iniciar el servidor
app.listen(port, () => {
  console.log(`Servidor escuchando en http://localhost:${port}`);
});
