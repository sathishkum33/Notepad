import java.security.KeyStore;
import java.security.Security;

public class KeystorePathPrinter {

    public static void main(String[] args) {
        try {
            String keystoreType = "JKS"; // Adjust the keystore type if necessary
            KeyStore keyStore = KeyStore.getInstance(keystoreType);

            String javaHome = System.getProperty("java.home");
            String cacertsPath = javaHome + "/lib/security/cacerts";

            System.out.println("Path to cacerts keystore: " + cacertsPath);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
