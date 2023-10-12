import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class HotelDatabaseManager {

    private static final String DB_URL = "jdbc:mysql://localhost:3306/hotel_db";
    private static final String DB_USER = "your_username";
    private static final String DB_PASSWORD = "your_password";

    public static void main(String[] args) {
        Connection connection = null;

        try {
            // Establish a connection to the database
            connection = DriverManager.getConnection(DB_URL, DB_USER, DB_PASSWORD);

            // Create a table to store hotel information if it doesn't exist
            createHotelTable(connection);

            // Insert a new hotel into the database
            insertHotel(connection, "Hotel ABC", "123 Main Street", "City", "Country", 4);

            // Retrieve and print information about hotels
            retrieveHotels(connection);

        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            try {
                if (connection != null) {
                    connection.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }

    private static void createHotelTable(Connection connection) throws SQLException {
        String createTableSQL = "CREATE TABLE IF NOT EXISTS hotels (" +
                "id INT AUTO_INCREMENT PRIMARY KEY," +
                "name VARCHAR(255) NOT NULL," +
                "address VARCHAR(255) NOT NULL," +
                "city VARCHAR(255) NOT NULL," +
                "country VARCHAR(255) NOT NULL," +
                "star_rating INT" +
                ");";

        PreparedStatement statement = connection.prepareStatement(createTableSQL);
        statement.execute();
    }

    private static void insertHotel(Connection connection, String name, String address, String city, String country, int starRating) throws SQLException {
        String insertSQL = "INSERT INTO hotels (name, address, city, country, star_rating) VALUES (?, ?, ?, ?, ?);";

        PreparedStatement statement = connection.prepareStatement(insertSQL);
        statement.setString(1, name);
        statement.setString(2, address);
        statement.setString(3, city);
        statement.setString(4, country);
        statement.setInt(5, starRating);

        statement.execute();
    }

    private static void retrieveHotels(Connection connection) throws SQLException {
        String selectSQL = "SELECT * FROM hotels;";
        PreparedStatement statement = connection.prepareStatement(selectSQL);
        ResultSet resultSet = statement.executeQuery();

        while (resultSet.next()) {
            int id = resultSet.getInt("id");
            String name = resultSet.getString("name");
            String address = resultSet.getString("address");
            String city = resultSet.getString("city");
            String country = resultSet.getString("country");
            int starRating = resultSet.getInt("star_rating");

            System.out.println("Hotel ID: " + id);
            System.out.println("Name: " + name);
            System.out.println("Address: " + address);
            System.out.println("City: " + city);
            System.out.println("Country: " + country);
            System.out.println("Star Rating: " + starRating);
            System.out.println();
        }
    }
}
