# Grocery-store-simulation
import java.util.*;

class Product {
    String name;
    double price;
    int quantity;

    public Product(String name, double price, int quantity) {
        this.name = name;
        this.price = price;
        this.quantity = quantity;
    }
}

class Customer {
    String name;
    double budget;
    Queue<String> shoppingList;
    LinkedList<String> shoppingCart;

    public Customer(String name, double budget) {
        this.name = name;
        this.budget = budget;
        this.shoppingList = new LinkedList<>();
        this.shoppingCart = new LinkedList<>();
    }

    public void addToShoppingList(String productName) {
        shoppingList.offer(productName);
    }
}

public class GroceryStoreSimulation {
    public static void main(String[] args) {
        Map<String, Product> inventory = new HashMap<>();
        inventory.put("Apples", new Product("Apples", 2.0, 10));
        inventory.put("Bananas", new Product("Bananas", 1.0, 15));
        inventory.put("Milk", new Product("Milk", 3.0, 5));
        inventory.put("Bread", new Product("Bread", 2.5, 8));
        // Add more products to the inventory

        Queue<Customer> customerQueue = new LinkedList<>();
        Random random = new Random();

        // Simulate customer arrivals
        for (int i = 1; i <= 5; i++) {
            String customerName = "Customer " + i;
            double customerBudget = random.nextDouble() * 100.0;

            Customer customer = new Customer(customerName, customerBudget);

            // Generate a random shopping list for the customer
            int numItems = random.nextInt(5) + 1;
            for (int j = 0; j < numItems; j++) {
                String randomProduct = getRandomProduct(inventory);
                customer.addToShoppingList(randomProduct);
            }

            customerQueue.offer(customer);
        }

        // Shopping simulation
        while (!customerQueue.isEmpty()) {
            Customer customer = customerQueue.poll();

            System.out.println(customer.name + " is shopping...");

            while (!customer.shoppingList.isEmpty()) {
                String productName = customer.shoppingList.poll();
                Product product = inventory.get(productName);

                if (product != null && product.quantity > 0 && product.price <= customer.budget) {
                    customer.shoppingCart.add(productName);
                    customer.budget -= product.price;
                    product.quantity--;

                    System.out.println(customer.name + " added " + productName + " to the cart.");
                } else {
                    System.out.println(customer.name + " cannot buy " + productName);
                }
            }

            // Checkout
            double totalCost = calculateTotalCost(customer.shoppingCart, inventory);
            customer.budget -= totalCost;
            System.out.println(customer.name + " is checking out. Total cost: $" + totalCost);
        }

        // Display updated inventory
        System.out.println("Updated Inventory:");
        for (Product product : inventory.values()) {
            System.out.println(product.name + ": Quantity=" + product.quantity);
        }
    }

    public static String getRandomProduct(Map<String, Product> inventory) {
        List<String> productList = new ArrayList<>(inventory.keySet());
        Random random = new Random();
        return productList.get(random.nextInt(productList.size()));
    }

    public static double calculateTotalCost(LinkedList<String> shoppingCart, Map<String, Product> inventory) {
        double totalCost = 0;
        for (String productName : shoppingCart) {
            Product product = inventory.get(productName);
            totalCost += product.price;
        }
        return totalCost;
    }
}
