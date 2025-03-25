import java.io.*;
import java.util.*;

class House implements Serializable {
    int id;
    String location;
    double price;
    int bedrooms;
    String owner;

    public House(int id, String location, double price, int bedrooms, String owner) {
        this.id = id;
        this.location = location;
        this.price = price;
        this.bedrooms = bedrooms;
        this.owner = owner;
    }

    public String toString() {
        return "ID: " + id + ", Location: " + location + ", Price: " + price + ", Bedrooms: " + bedrooms + ", Owner: " + owner;
    }
}

class Tenant implements Serializable {
    String name;
    String contact;
    String preferredLocation;

    public Tenant(String name, String contact, String preferredLocation) {
        this.name = name;
        this.contact = contact;
        this.preferredLocation = preferredLocation;
    }

    public String toString() {
        return "Name: " + name + ", Contact: " + contact + ", Preferred Location: " + preferredLocation;
    }
}

class RentalSystem {
    List<House> houses = new ArrayList<>();
    List<Tenant> tenants = new ArrayList<>();

    public void addHouse(House house) {
        houses.add(house);
        saveData();
    }

    public void removeHouse(int houseId) {
        houses.removeIf(h -> h.id == houseId);
        saveData();
    }

    public void registerTenant(Tenant tenant) {
        tenants.add(tenant);
        saveData();
    }

    public void searchHouses(String location, double minPrice, double maxPrice) {
        for (House h : houses) {
            if (h.location.equalsIgnoreCase(location) && h.price >= minPrice && h.price <= maxPrice) {
                System.out.println(h);
            }
        }
    }

    public void saveData() {
        try (ObjectOutputStream out = new ObjectOutputStream(new FileOutputStream("houses.txt"))) {
            out.writeObject(houses);
        } catch (IOException e) {
            e.printStackTrace();
        }
        try (ObjectOutputStream out = new ObjectOutputStream(new FileOutputStream("tenants.txt"))) {
            out.writeObject(tenants);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    @SuppressWarnings("unchecked")
    public void loadData() {
        try (ObjectInputStream in = new ObjectInputStream(new FileInputStream("houses.txt"))) {
            houses = (List<House>) in.readObject();
        } catch (IOException | ClassNotFoundException e) {
            houses = new ArrayList<>();
        }
        try (ObjectInputStream in = new ObjectInputStream(new FileInputStream("tenants.txt"))) {
            tenants = (List<Tenant>) in.readObject();
        } catch (IOException | ClassNotFoundException e) {
            tenants = new ArrayList<>();
        }
    }
}

public class HouseRentalApp {
    public static void main(String[] args) {
        RentalSystem system = new RentalSystem();
        system.loadData();
        Scanner scanner = new Scanner(System.in);
        
        while (true) {
            System.out.println("1. Add House\n2. Remove House\n3. Search Houses\n4. Register Tenant\n5. Exit");
            int choice = scanner.nextInt();
            scanner.nextLine();
            
            switch (choice) {
                case 1:
                    System.out.print("Enter ID: ");
                    int id = scanner.nextInt();
                    scanner.nextLine();
                    System.out.print("Enter Location: ");
                    String location = scanner.nextLine();
                    System.out.print("Enter Price: ");
                    double price = scanner.nextDouble();
                    System.out.print("Enter Bedrooms: ");
                    int bedrooms = scanner.nextInt();
                    scanner.nextLine();
                    System.out.print("Enter Owner: ");
                    String owner = scanner.nextLine();
                    system.addHouse(new House(id, location, price, bedrooms, owner));
                    break;
                case 2:
                    System.out.print("Enter House ID to Remove: ");
                    int houseId = scanner.nextInt();
                    system.removeHouse(houseId);
                    break;
                case 3:
                    System.out.print("Enter Location: ");
                    String searchLocation = scanner.nextLine();
                    System.out.print("Enter Min Price: ");
                    double minPrice = scanner.nextDouble();
                    System.out.print("Enter Max Price: ");
                    double maxPrice = scanner.nextDouble();
                    system.searchHouses(searchLocation, minPrice, maxPrice);
                    break;
                case 4:
                    System.out.print("Enter Name: ");
                    String name = scanner.nextLine();
                    System.out.print("Enter Contact: ");
                    String contact = scanner.nextLine();
                    System.out.print("Enter Preferred Location: ");
                    String preferredLocation = scanner.nextLine();
                    system.registerTenant(new Tenant(name, contact, preferredLocation));
                    break;
                case 5:
                    scanner.close();
                    return;
                default:
                    System.out.println("Invalid option.");
            }
        }
    }
}

