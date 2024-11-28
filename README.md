package controller;

import java.util.ArrayList;
import java.util.Hashtable;
import java.util.Random;
import model.Fruit;
import model.OrderItem;
import util.Inputer;

/**
 * Controller class for managing the Fruit Shop system.
 */
public class FruitShopController {

    private ArrayList<Fruit> fruits;
    private Hashtable<String, ArrayList<OrderItem>> orders;

    /**
     * Initializes the Fruit Shop with sample data.
     */
    public FruitShopController() {
        fruits = new ArrayList<>();
        orders = new Hashtable<>();
        initializeSampleData();
    }

    /**
     * Adds some sample fruits to the shop for testing purposes.
     */
    private void initializeSampleData() {
        fruits.add(new Fruit("F001", "Apple", 1.5, 10, "USA"));
        fruits.add(new Fruit("F002", "Banana", 0.5, 10, "Vietnam"));
        fruits.add(new Fruit("F003", "Orange", 2.0, 10, "Thailand"));
    }

    /**
     * Displays the main menu and returns the user's choice.
     *
     * @return the user's choice as an integer.
     */
    public int displayMenu() {
        while (true) {
            System.out.println("\nFRUIT SHOP SYSTEM");
            System.out.println("1. Create Fruit");
            System.out.println("2. View List of Fruits");
            System.out.println("3. View Orders");
            System.out.println("4. Shopping");
            System.out.println("5. Exit");
            int choice = Inputer.inputInt("Please choose an option (1-5): ", 1, 5);
            return choice;
        }
    }

    /**
     * Checks if a fruit ID already exists in the list.
     *
     * @param id the fruit ID to check.
     * @return true if the ID exists, false otherwise.
     */
    private boolean isFruitIdExists(String id) {
        boolean exist = false;
        for (Fruit fruit : fruits) {
            if (fruit.getId().equalsIgnoreCase(id)) {
                exist = true;
                break;
            }
        }
        return exist;
    }

    /**
     * Handles the creation of new fruits by the shop owner.
     */
    public void createFruit() {
        while (true) {
            String id = Inputer.inputString("Enter Fruit ID: ");

            // Check if the ID already exists in the list
            if (isFruitIdExists(id)) {
                System.out.println("Fruit ID already exists. Please enter a different ID.");
                continue;
            }

            String name = Inputer.inputString("Enter Fruit Name: ");
            double price = Inputer.inputDouble("Enter Fruit Price: ", 1, Integer.MAX_VALUE);
            int quantity = Inputer.inputInt("Enter Quantity: ", 1, Integer.MAX_VALUE);
            String origin = Inputer.inputString("Enter Origin: ");
            fruits.add(new Fruit(id, name, price, quantity, origin));
            System.out.println("Fruit added successfully!");

            String continueChoice = Inputer.inputYesNo("Do you want to add another fruit?");
            if (continueChoice.equals("N")) {
                break;
            }
        }
    }

    /**
     * Displays the list of fruits in the shop.
     */
    public void viewFruits() {
        if (fruits.isEmpty()) {
            System.out.println("No fruits available in the shop.");
            return;
        }
        System.out.println("\nList of Fruits:");
        System.out.printf("%-10s | %-10s | %-10s | %-10s | %-10s\n",
                "ID", "Name", "Price", "Quantity", "Origin");
        for (Fruit fruit : fruits) {
            System.out.printf("%-10s | %-10s | %-10.2f | %-10d | %-10s\n",
                    fruit.getId(), fruit.getName(), fruit.getPrice(), fruit.getQuantity(), fruit.getOrigin());
        }
    }

    /**
     * Displays all orders placed by customers.
     */
    public void viewOrders() {
        if (orders.isEmpty()) {
            System.out.println("No orders have been placed yet.");
            return;
        }
        for (String customer : orders.keySet()) {
            System.out.println("\nCustomer: " + customer);
            ArrayList<OrderItem> orderItems = orders.get(customer);
            double total = 0;
            for (OrderItem item : orderItems) {
                System.out.printf("%-15s | %-5d | %-5.2f$ | %-5.2f$\n",
                        item.getFruit().getName(),
                        item.getQuantity(),
                        item.getFruit().getPrice(),
                        item.getTotalPrice());
                total += item.getTotalPrice();
            }
            System.out.printf("Total: %.2f$\n", total);
        }
    }

    /**
     * Handles the shopping process for customers.
     */
    public void shopping() {
        if (fruits.isEmpty()) {
            System.out.println("No fruits available for shopping.");
            return;
        }

        ArrayList<OrderItem> cart = new ArrayList<>();
        while (true) {
            System.out.println("\nList of Fruits:");
            for (int i = 0; i < fruits.size(); i++) {
                Fruit fruit = fruits.get(i);
                System.out.printf("%d. %-10s | %-10s | %.2f$\n",
                        i + 1, fruit.getName(), fruit.getOrigin(), fruit.getPrice());
            }

            int choice = Inputer.inputInt("Select a fruit to buy (1-" + fruits.size() + "): ", 1, fruits.size());
            Fruit selectedFruit = fruits.get(choice - 1);
            System.out.println("You selected: " + selectedFruit.getName());

            int quantity = Inputer.inputInt("Enter quantity to buy: ", 1, selectedFruit.getQuantity());
            selectedFruit.setQuantity(selectedFruit.getQuantity() - quantity);

            if (selectedFruit.getQuantity() == 0) {
                fruits.remove(selectedFruit);
            }

            // Kiểm tra xem sản phẩm đã có trong giỏ hàng chưa
            boolean found = false;
            for (OrderItem item : cart) {
                if (item.getFruit().equals(selectedFruit)) {
                    item.setQuantity(item.getQuantity() + quantity); // Cập nhật số lượng
                    found = true;
                    break;
                }
            }

            // Nếu sản phẩm chưa có trong giỏ hàng, thêm mới vào giỏ
            if (!found) {
                cart.add(new OrderItem(selectedFruit, quantity));
            }

            String continueChoice = Inputer.inputYesNo("Do you want to continue shopping?");
            if (continueChoice.equalsIgnoreCase("N")) {
                break;
            }
        }

        // Lấy tên khách hàng
        String customerName = Inputer.inputString("Enter your name to complete the order: ");

        // Kiểm tra tên khách hàng có trùng không và thêm khoảng trắng nếu cần
        if (orders.containsKey(customerName)) {
            Random random = new Random();
            int numberOfSpace = random.nextInt(10);
            for (int i = 0; i < numberOfSpace; i++) {
                customerName += " ";
            }
        }

        // Lưu giỏ hàng với tên khách hàng
        orders.put(customerName, cart);
        System.out.println("Thank you for your purchase!");
    }

}
