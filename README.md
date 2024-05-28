import javax.net.ssl.SSLSocketFactory;

public class CipherCheck {
    public static void main(String[] args) {
        // Get the default SSL socket factory
        SSLSocketFactory factory = (SSLSocketFactory) SSLSocketFactory.getDefault();
        
        // Get the supported cipher suites
        String[] supportedCiphers = factory.getSupportedCipherSuites();
        
        // Check if the desired cipher suite is supported
        String desiredCipher = "ECDHE-RSA-AES256-GCM-SHA384";
        boolean isSupported = false;
        
        for (String cipher : supportedCiphers) {
            if (cipher.equals(desiredCipher)) {
                isSupported = true;
                break;
            }
        }
        
        if (isSupported) {
            System.out.println("Cipher suite " + desiredCipher + " is supported.");
        } else {
            System.out.println("Cipher suite " + desiredCipher + " is NOT supported.");
        }
    }
}
