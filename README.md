# Airline-management-system-
It's like how the air management system works like tickt booking 
package Sem3.Java;

import java.util.InputMismatchException;
import java.util.Scanner;
import java.util.UUID; // For generating unique PNR numbers

class Flight {
    String flightNumber;
    String destination;
    String source;
    String type; // Domestic or International
    double baseFare;

    public Flight(String flightNumber, String source, String destination, String type, double baseFare) {
        this.flightNumber = flightNumber;
        this.source = source;
        this.destination = destination;
        this.type = type;
        this.baseFare = baseFare;
    }

    public void displayFlightDetails() {
        System.out.println("Flight Number: " + flightNumber);
        System.out.println("Source: " + source);
        System.out.println("Destination: " + destination);
        System.out.println("Flight Type: " + type);
        System.out.println("Base Fare: ₹" + baseFare);
    }

    public double calculateFare(String travelClass) {
        double finalFare = baseFare;
        switch (travelClass.toLowerCase()) {
            case "economy":
                finalFare += 0; // No additional charges for economy
                break;
            case "business":
                finalFare += 5000; // Additional charges for business class
                break;
            case "first":
                finalFare += 10000; // Additional charges for first class
                break;
            default:
                System.out.println("Invalid travel class, defaulting to Economy.");
                break;
        }
        return finalFare;
    }
}

class Passenger {
    String name;
    int age;
    String gender;
    String contactNumber;

    public Passenger(String name, int age, String gender, String contactNumber) {
        this.name = name;
        this.age = age;
        this.gender = gender;
        this.contactNumber = contactNumber;
    }

    public void displayPassengerDetails() {
        System.out.println("Passenger Name: " + name);
        System.out.println("Age: " + age);
        System.out.println("Gender: " + gender);
        System.out.println("Contact Number: " + contactNumber);
    }
}

class Booking {
    Flight flight;
    Passenger passenger;
    String travelClass;
    double fare;
    String pnr;

    public Booking(Flight flight, Passenger passenger, String travelClass, double fare, String pnr) {
        this.flight = flight;
        this.passenger = passenger;
        this.travelClass = travelClass;
        this.fare = fare;
        this.pnr = pnr;
    }

    public void displayBookingDetails() {
        System.out.println("\nBooking Details:");
        flight.displayFlightDetails();
        System.out.println("Travel Class: " + travelClass);
        System.out.println("Total Fare: ₹" + fare);
        System.out.println("PNR Number: " + pnr);
        System.out.println();
        passenger.displayPassengerDetails();
    }
}

public class AirlineSystem {
    static Flight[] availableFlights = {
        new Flight("AI101", "Mumbai", "Delhi", "Domestic", 5000),
        new Flight("AI102", "Bangalore", "Paris", "International", 35000),
        new Flight("AI103", "Chennai", "Singapore", "International", 25000),
        new Flight("AI104", "Kolkata", "Hyderabad", "Domestic", 4000)
    };
    
    static Booking currentBooking = null;

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        boolean exit = false;

        while (!exit) {
            System.out.println("\n--- Airline Reservation System ---");
            System.out.println("1. View All Available Flights");
            System.out.println("2. Book a Flight");
            System.out.println("3. View Your Booking");
            System.out.println("4. Exit");
            System.out.print("Enter your choice: ");
            
            int choice = 0;
            try {
                choice = sc.nextInt();
            } catch (InputMismatchException e) {
                System.out.println("Invalid input. Please enter a number.");
                sc.next(); // Clear the invalid input
                continue;
            }

            switch (choice) {
                case 1:
                    viewAllFlights();
                    break;

                case 2:
                    bookFlight(sc);
                    break;

                case 3:
                    viewBooking();
                    break;

                case 4:
                    System.out.println("Exiting the system. Thank you!");
                    exit = true;
                    break;

                default:
                    System.out.println("Invalid choice. Please select a valid option.");
                    break;
            }
        }

        sc.close();
    }

    // View all available flights
    public static void viewAllFlights() {
        System.out.println("\nAvailable Flights:");
        for (Flight flight : availableFlights) {
            flight.displayFlightDetails();
            System.out.println();
        }
    }

    // Book a flight
    public static void bookFlight(Scanner sc) {
        if (currentBooking != null) {
            System.out.println("You already have an active booking.");
            return;
        }

        // Step 1: Enter passenger details
        sc.nextLine(); // Consume newline
        System.out.print("Enter passenger name: ");
        String name = sc.nextLine();
        System.out.print("Enter passenger age: ");
        int age = sc.nextInt();
        System.out.print("Enter passenger gender (M/F): ");
        String gender = sc.next();
        System.out.print("Enter contact number: ");
        String contactNumber = sc.next();

        Passenger passenger = new Passenger(name, age, gender, contactNumber);

        // Step 2: Display available flights and select one
        System.out.println("\nAvailable Flights:");
        for (int i = 0; i < availableFlights.length; i++) {
            System.out.println((i + 1) + ". " + availableFlights[i].flightNumber + " (" + availableFlights[i].source + " to " + availableFlights[i].destination + ")");
        }

        int flightChoice = 0;
        boolean validFlightChoice = false;
        while (!validFlightChoice) {
            try {
                System.out.print("\nSelect the flight (Enter the flight number): ");
                flightChoice = sc.nextInt();
                if (flightChoice < 1 || flightChoice > availableFlights.length) {
                    System.out.println("Invalid flight number. Please select a valid flight number.");
                } else {
                    validFlightChoice = true;
                }
            } catch (InputMismatchException e) {
                System.out.println("Invalid input. Please enter a number.");
                sc.next(); // Clear the invalid input
            }
        }

        Flight selectedFlight = availableFlights[flightChoice - 1];

        // Step 3: Choose travel class
        System.out.print("\nChoose your travel class (Economy/Business/First): ");
        String travelClass = sc.next();

        // Step 4: Calculate fare
        double ticketPrice = selectedFlight.calculateFare(travelClass);
        System.out.println("Total Fare: ₹" + ticketPrice);

        // Step 5: Payment process with validation
        double payment = 0;
        boolean validPayment = false;
        while (!validPayment) {
            try {
                System.out.print("\nEnter payment amount: ₹");
                payment = sc.nextDouble();
                if (payment < ticketPrice) {
                    System.out.println("Insufficient payment! Please enter a valid payment amount.");
                } else {
                    validPayment = true;
                }
            } catch (InputMismatchException e) {
                System.out.println("Invalid input. Please enter a valid amount.");
                sc.next(); // Clear the invalid input
            }
        }

        // Payment successful, generate PNR
        if (payment >= ticketPrice) {
            double balance = payment - ticketPrice;
            if (balance > 0) {
                System.out.println("Payment successful! Balance refunded: ₹" + balance);
            } else {
                System.out.println("Payment successful!");
            }

            // Step 6: Generate PNR and create booking
            String pnrNumber = generatePNR();
            currentBooking = new Booking(selectedFlight, passenger, travelClass, ticketPrice, pnrNumber);
            System.out.println("\nYour booking is confirmed!");
            System.out.println("PNR Number: " + pnrNumber);
        }
    }

    // View current booking
    public static void viewBooking() {
        if (currentBooking == null) {
            System.out.println("No active bookings found.");
        } else {
            currentBooking.displayBookingDetails();
        }
    }

    // Method to generate a unique PNR number
    public static String generatePNR() {
        return UUID.randomUUID().toString().substring(0, 8).toUpperCase();
    }
}
