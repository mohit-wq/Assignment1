package com.careercamp.dto;

public class Product {
    private String prodId;
    private String prodName;
    private double price;
    private int quantity;
    private double discount;

    // Constructor, getters, and setters

    @Override
    public String toString() {
        return "Product [prodId=" + prodId + ", prodName=" + prodName + ", price=" + price + ", quantity=" + quantity
                + ", discount=" + discount + "]";
    }
}

***********************************************************************************************
package com.careercamp.service;

public class ProductNotFoundException extends Exception {
    public ProductNotFoundException(String message) {
        super(message);
    }
}

******************************************************************************************
package com.careercamp.dao;

import com.careercamp.dto.Product;
import com.careercamp.service.ProductNotFoundException;

public interface ProductDao {
    void addProduct(Product product);

    void deleteProduct(String prodId) throws ProductNotFoundException;

    void updateProduct(Product product) throws ProductNotFoundException;

    Product searchProductById(String prodId) throws ProductNotFoundException;

    Product searchProductByName(String prodName) throws ProductNotFoundException;

    Product[] getAllProducts();

    Product[] getProductsByPriceOrder(boolean ascending);

    Product[] getProductsByDiscountOrder(boolean ascending);
}
************************************************************************************************
****************************************************************************************************
package com.careercamp.dao;

import com.careercamp.dto.Product;
import com.careercamp.service.ProductNotFoundException;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.List;

public class InMemoryProductDao implements ProductDao {
    private List<Product> products;

    public InMemoryProductDao() {
        this.products = new ArrayList<>();
    }

    @Override
    public void addProduct(Product product) {
        products.add(product);
    }

    @Override
    public void deleteProduct(String prodId) throws ProductNotFoundException {
        boolean removed = products.removeIf(p -> p.getProdId().equals(prodId));
        if (!removed) {
            throw new ProductNotFoundException("Product with ID " + prodId + " not found.");
        }
    }

    @Override
    public void updateProduct(Product product) throws ProductNotFoundException {
        int index = getProductIndex(product.getProdId());
        if (index != -1) {
            products.set(index, product);
        } else {
            throw new ProductNotFoundException("Product with ID " + product.getProdId() + " not found.");
        }
    }

    @Override
    public Product searchProductById(String prodId) throws ProductNotFoundException {
        int index = getProductIndex(prodId);
        if (index != -1) {
            return products.get(index);
        } else {
            throw new ProductNotFoundException("Product with ID " + prodId + " not found.");
        }
    }

    @Override
    public Product searchProductByName(String prodName) throws ProductNotFoundException {
        for (Product product : products) {
            if (product.getProdName().equals(prodName)) {
                return product;
            }
        }
        throw new ProductNotFoundException("Product with name '" + prodName + "' not found.");
    }

    @Override
    public Product[] getAllProducts() {
        return products.toArray(new Product[0]);
    }

    @Override
    public Product[] getProductsByPriceOrder(boolean ascending) {
        List<Product> sortedProducts = new ArrayList<>(products);
        sortedProducts.sort((p1, p2) -> {
            if (ascending) {
                return Double.compare(p1.getPrice(), p2.getPrice());
            } else {
                return
