# java-project
import java.util.*;

// ---------- Product Class ----------
class Product {
    private int id;
    private String name;
    private double price;
    private int stock;

    public Product(int id, String name, double price, int stock) {
        this.id = id;
        this.name = name;
        this.price = price;
        this.stock = stock;
    }

    public String getName() { return name; }
    public double getPrice() { return price; }
    public int getStock() { return stock; }
    public int getId() { return id; }

    public void reduceStock(int qty) {
        if (qty <= stock) {
            stock -= qty;
        }
    }

    public void showDetails() {
        System.out.println(id + ". " + name + " (₹" + price + ", Stock: " + stock + ")");
    }
}

// ---------- CartItem Class ----------
class CartItem {
    private Product product;
    private int quantity;

    public CartItem(Product product, int quantity) {
        this.product = product;
        this.quantity = quantity;
    }

    public Product getProduct() { return product; }
    public int getQuantity() { return quantity; }
    public double getSubtotal() {
        return product.getPrice() * quantity;
    }
}

// ---------- Cart Class ----------
class Cart {
    private List<CartItem> items = new ArrayList<>();

    public void addItem(Product p, int qty) {
        if (p.getStock() >= qty) {
            items.add(new CartItem(p, qty));
            p.reduceStock(qty);
            System.out.println(qty + " x " + p.getName() + " added to cart.");
        } else {
            System.out.println("❌ Not enough stock for " + p.getName());
        }
    }

    public void viewCart() {
        if (items.isEmpty()) {
            System.out.println("🛒 Cart is empty!");
            return;
        }
        System.out.println("\n🛒 Cart Contents:");
        double total = 0;
        for (CartItem item : items) {
            System.out.println(item.getProduct().getName() + " x " + item.getQuantity() +
                    " = ₹" + item.getSubtotal());
            total += item.getSubtotal();
        }
        System.out.println("Cart Total: ₹" + total);
    }

    public double calculateTotal() {
        double total = 0;
        for (CartItem item : items) {
            total += item.getSubtotal();
        }
        return total;
    }

    public List<CartItem> getItems() {
        return items;
    }

    public boolean isEmpty() {
        return items.isEmpty();
    }
}

// ---------- Customer Class ----------
class Customer {
    private int id;
    private String name;
    private Cart cart;

    public Customer(int id, String name) {
        this.id = id;
        this.name = name;
        this.cart = new Cart();
    }

    public void addToCart(Product p, int qty) {
        cart.addItem(p, qty);
    }

    public void viewCart() {
        cart.viewCart();
    }

    public Cart getCart() {
        return cart;
    }

    public String getName() { return name; }
}

// ---------- Order Class ----------
class Order {
    private int orderId;
    private Customer customer;
    private List<CartItem> items;
    private double totalAmount;

    public Order(int orderId, Customer customer, List<CartItem> items) {
        this.orderId = orderId;
        this.customer = customer;
        this.items = items;
        this.totalAmount = calculateTotal();
    }

    private double calculateTotal() {
        double total = 0;
        for (CartItem item : items) {
            total += item.getSubtotal();
        }
        return total;
    }

    public void applyDiscount(double percent) {
        double discount = (percent / 100) * totalAmount;
        totalAmount -= discount;
        System.out.println("🎉 Discount Applied: " + percent + "% (-₹" + discount + ")");
    }

    public void generateInvoice() {
        System.out.println("\n========== INVOICE ==========");
        System.out.println("Order ID: " + orderId);
        System.out.println("Customer: " + customer.getName());
        System.out.println("----------------------------");

        for (CartItem item : items) {
            System.out.println(item.getProduct().getName() + " x " + item.getQuantity() +
                    " = ₹" + item.getSubtotal());
        }

        System.out.println("----------------------------");
        System.out.println("Final Amount: ₹" + totalAmount);
        System.out.println("============================");
    }
}

// ---------- Main App ----------
public class ECommerceApp {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        // Create products
        List<Product> products = new ArrayList<>();
        products.add(new Product(1, "Laptop", 60000, 5));
        products.add(new Product(2, "Phone", 30000, 10));
        products.add(new Product(3, "Headphones", 2000, 20));

        // Customer
        System.out.print("Enter Customer Name: ");
        String custName = sc.nextLine();
        Customer customer = new Customer(101, custName);

        boolean shopping = true;
        while (shopping) {
            System.out.println("\n📦 Available Products:");
            for (Product p : products) {
                p.showDetails();
            }

            System.out.print("\nEnter product ID to add to cart (0 to checkout): ");
            int choice = sc.nextInt();

            if (choice == 0) {
                shopping = false;
                break;
            }

            Product selected = null;
            for (Product p : products) {
                if (p.getId() == choice) {
                    selected = p;
                    break;
                }
            }

            if (selected == null) {
                System.out.println("❌ Invalid product ID!");
                continue;
            }

            System.out.print("Enter quantity: ");
            int qty = sc.nextInt();

            customer.addToCart(selected, qty);
            customer.viewCart();
        }

        // Checkout
        if (customer.getCart().isEmpty()) {
            System.out.println("Cart is empty. Exiting...");
        } else {
            Order order = new Order(5001, customer, customer.getCart().getItems());

            System.out.print("\nEnter discount percentage (0 if none): ");
            double discount = sc.nextDouble();
            if (discount > 0) {
                order.applyDiscount(discount);
            }

            order.generateInvoice();
        }

        sc.close();
    }
}
