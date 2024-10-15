import java.util.*;

// Clase que representa a un usuario
class User {
    private int id;
    private Map<Product, Integer> ratings;

    public User(int id) {
        this.id = id;
        this.ratings = new HashMap<>();
    }

    public void addRating(Product product, int rating) {
        ratings.put(product, rating);
    }

    public Map<Product, Integer> getRatings() {
        return ratings;
    }
}

// Clase que representa a un producto
class Product {
    private int id;
    private Map<User, Integer> ratings;

    public Product(int id) {
        this.id = id;
        this.ratings = new HashMap<>();
    }

    public void addRating(User user, int rating) {
        ratings.put(user, rating);
    }

    public Map<User, Integer> getRatings() {
        return ratings;
    }
}

// Clase que se encarga de generar las recomendaciones
class Recommender {
    private List<User> users;
    private List<Product> products;

    public Recommender(List<User> users, List<Product> products) {
        this.users = users;
        this.products = products;
    }

    // Método que genera recomendaciones para un usuario
    public List<Product> generateRecommendations(User user) {
        List<Product> recommendedProducts = new ArrayList<>();

        // Calcula la similitud entre el usuario y otros usuarios
        for (User otherUser : users) {
            if (otherUser != user) {
                double similarity = calculateSimilarity(user, otherUser);
                if (similarity > 0) {
                    // Agrega productos que el otro usuario ha calificado bien
                    for (Product product : otherUser.getRatings().keySet()) {
                        if (!user.getRatings().containsKey(product)) {
                            recommendedProducts.add(product);
                        }
                    }
                }
            }
        }

        return recommendedProducts;
    }

    // Método que calcula la similitud entre dos usuarios
    private double calculateSimilarity(User user1, User user2) {
        double sum = 0;
        int count = 0;

        for (Product product : user1.getRatings().keySet()) {
            if (user2.getRatings().containsKey(product)) {
                sum += user1.getRatings().get(product) * user2.getRatings().get(product);
                count++;
            }
        }

        if (count == 0) {
            return 0;
        }

        return sum / count;
    }
}

public class Main {
    public static void main(String[] args) {
        // Crea usuarios y productos
        User user1 = new User(1);
        User user2 = new User(2);
        User user3 = new User(3);

        Product productA = new Product(1);
        Product productB = new Product(2);
        Product productC = new Product(3);

        // Agrega calificaciones
        user1.addRating(productA, 5);
        user1.addRating(productB, 3);
        user2.addRating(productA, 4);
        user2.addRating(productC, 5);
        user3.addRating(productB, 4);
        user3.addRating(productC, 3);

        // Crea un recomendador
        List<User> users = Arrays.asList(user1, user2, user3);
        List<Product> products = Arrays.asList(productA, productB, productC);
        Recommender recommender = new Recommender(users, products);

        // Genera recomendaciones para el usuario 1
        List<Product> recommendedProducts = recommender.generateRecommendations(user1);

        // Imprime las recomendaciones
        System.out.println("Recomendaciones para el usuario 1:");
        for (Product product : recommendedProducts) {
            System.out.println(product.id);
        }
    }
}
