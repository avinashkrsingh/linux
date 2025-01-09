import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.jfree.chart.ChartFactory;
import org.jfree.chart.ChartPanel;
import org.jfree.chart.JFreeChart;
import org.jfree.chart.plot.PlotOrientation;
import org.jfree.data.category.DefaultCategoryDataset;
import org.jfree.data.general.DefaultPieDataset;
import org.jfree.ui.ApplicationFrame;
import org.jfree.ui.RefineryUtilities;

import javax.swing.*;
import java.awt.*;
import java.io.File;
import java.io.IOException;
import java.util.Iterator;

public class SnykAdvancedVisualization extends ApplicationFrame {

    public SnykAdvancedVisualization(String title) {
        super(title);
    }

    // Method to create a bar chart for vulnerabilities by severity
    public JFreeChart createBarChart(DefaultCategoryDataset dataset) {
        JFreeChart chart = ChartFactory.createBarChart(
                "Vulnerabilities by Severity", // Chart title
                "Severity",                    // X-axis Label
                "Count",                       // Y-axis Label
                dataset,                       // Dataset
                PlotOrientation.VERTICAL,      // Plot orientation
                true,                          // Include legend
                true,                          // Tooltips
                false                          // URLs
        );
        // Customize the chart
        chart.setBackgroundPaint(Color.white);
        return chart;
    }

    // Method to create a pie chart for vulnerabilities distribution
    public JFreeChart createPieChart(DefaultPieDataset dataset) {
        JFreeChart pieChart = ChartFactory.createPieChart(
                "Vulnerabilities Distribution", // Chart title
                dataset,                       // Dataset
                true,                          // Include legend
                true,                          // Tooltips
                false                          // URLs
        );
        // Customize the pie chart
        pieChart.setBackgroundPaint(Color.white);
        return pieChart;
    }

    // Method to create the dataset for a bar chart
    private DefaultCategoryDataset createBarDataset(JsonNode vulnerabilities) {
        DefaultCategoryDataset dataset = new DefaultCategoryDataset();

        // Initialize counters for each severity
        int lowCount = 0, mediumCount = 0, highCount = 0, criticalCount = 0;

        // Loop through the vulnerabilities and count by severity
        Iterator<JsonNode> elements = vulnerabilities.elements();
        while (elements.hasNext()) {
            JsonNode vulnerability = elements.next();
            String severity = vulnerability.get("severity").asText();

            switch (severity.toLowerCase()) {
                case "low":
                    lowCount++;
                    break;
                case "medium":
                    mediumCount++;
                    break;
                case "high":
                    highCount++;
                    break;
                case "critical":
                    criticalCount++;
                    break;
            }
        }

        // Add counts to the dataset
        dataset.addValue(lowCount, "Severity", "Low");
        dataset.addValue(mediumCount, "Severity", "Medium");
        dataset.addValue(highCount, "Severity", "High");
        dataset.addValue(criticalCount, "Severity", "Critical");

        return dataset;
    }

    // Method to create the dataset for a pie chart
    private DefaultPieDataset createPieDataset(JsonNode vulnerabilities) {
        DefaultPieDataset dataset = new DefaultPieDataset();

        // Initialize counters for each severity
        int lowCount = 0, mediumCount = 0, highCount = 0, criticalCount = 0;

        // Loop through the vulnerabilities and count by severity
        Iterator<JsonNode> elements = vulnerabilities.elements();
        while (elements.hasNext()) {
            JsonNode vulnerability = elements.next();
            String severity = vulnerability.get("severity").asText();

            switch (severity.toLowerCase()) {
                case "low":
                    lowCount++;
                    break;
                case "medium":
                    mediumCount++;
                    break;
                case "high":
                    highCount++;
                    break;
                case "critical":
                    criticalCount++;
                    break;
            }
        }

        // Add data to pie chart
        dataset.setValue("Low", lowCount);
        dataset.setValue("Medium", mediumCount);
        dataset.setValue("High", highCount);
        dataset.setValue("Critical", criticalCount);

        return dataset;
    }

    // Method to parse the Snyk JSON report
    private JsonNode parseSnykReport(String filePath) throws IOException {
        ObjectMapper objectMapper = new ObjectMapper();
        JsonNode rootNode = objectMapper.readTree(new File(filePath));
        return rootNode.get("vulnerabilities");
    }

    // Main method
    public static void main(String[] args) {
        try {
            // Run snyk test to generate the JSON report
            Process process = new ProcessBuilder("snyk", "test", "--json").start();
            process.waitFor();

            // Parse the Snyk JSON report
            SnykAdvancedVisualization chartApp = new SnykAdvancedVisualization("Snyk Vulnerability Report");
            JsonNode vulnerabilities = chartApp.parseSnykReport("snyk-report.json");

            // Create datasets for the charts
            DefaultCategoryDataset barDataset = chartApp.createBarDataset(vulnerabilities);
            DefaultPieDataset pieDataset = chartApp.createPieDataset(vulnerabilities);

            // Create the bar chart and pie chart
            JFreeChart barChart = chartApp.createBarChart(barDataset);
            JFreeChart pieChart = chartApp.createPieChart(pieDataset);

            // Create panels for the charts
            ChartPanel barChartPanel = new ChartPanel(barChart);
            barChartPanel.setPreferredSize(new java.awt.Dimension(600, 400));

            ChartPanel pieChartPanel = new ChartPanel(pieChart);
            pieChartPanel.setPreferredSize(new java.awt.Dimension(600, 400));

            // Create a panel to hold the charts
            JPanel chartPanel = new JPanel(new GridLayout(1, 2));
            chartPanel.add(barChartPanel);
            chartPanel.add(pieChartPanel);

            // Set the layout for the main window and add the charts
            chartApp.setContentPane(chartPanel);

            // Show the window
            chartApp.pack();
            RefineryUtilities.centerFrameOnScreen(chartApp);
            chartApp.setVisible(true);

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
