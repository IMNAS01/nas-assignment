import java.io.*;
import java.util.*;
import org.json.JSONObject;

public class SecretSharingSolver {

    
    public static long convertToDecimal(String value, int base) {
        return Long.parseLong(value, base);
    }

    // Read JSON file and extract (x, y) pairs
    public static List<double[]> readJsonInput(String filename) throws Exception {
        List<double[]> points = new ArrayList<>();
        File file = new File(filename);
        BufferedReader br = new BufferedReader(new FileReader(file));
        StringBuilder jsonText = new StringBuilder();
        String line;

        while ((line = br.readLine()) != null) {
            jsonText.append(line);
        }
        br.close();

        JSONObject inputJson = new JSONObject(jsonText.toString());
        JSONObject keys = inputJson.getJSONObject("keys");
        int n = keys.getInt("n");
        int k = keys.getInt("k");

        for (String key : inputJson.keySet()) {
            if (key.equals("keys")) continue;

            JSONObject valueObj = inputJson.getJSONObject(key);
            int x = Integer.parseInt(key);
            int base = valueObj.getInt("base");
            String value = valueObj.getString("value");
            long y = convertToDecimal(value, base);

            points.add(new double[]{x, y});
        }

        return points;
    }

    
    public static double lagrangeInterpolation(List<double[]> points) {
        int k = points.size();
        double constantTerm = 0;

        for (int j = 0; j < k; j++) {
            double Lj = 1.0;
            for (int i = 0; i < k; i++) {
                if (i != j) {
                    Lj *= (0 - points.get(i)[0]) / (points.get(j)[0] - points.get(i)[0]);
                }
            }
            constantTerm += points.get(j)[1] * Lj;
        }

        return Math.round(constantTerm);
    }

    public static void main(String[] args) {
        try {
            
            List<double[]> testCase1 = readJsonInput("testcase1.json");
            double secret1 = lagrangeInterpolation(testCase1);
            System.out.println("Secret for Test Case 1: " + (long) secret1);

            
            List<double[]> testCase2 = readJsonInput("testcase2.json");
            double secret2 = lagrangeInterpolation(testCase2);
            System.out.println("Secret for Test Case 2: " + (long) secret2);

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
