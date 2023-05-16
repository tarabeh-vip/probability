# probability
using System;
using System.Collections.Generic;
using System.Linq;

namespace StatisticsCalculator
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.Write("Enter the number of items: ");
            int n = int.Parse(Console.ReadLine());

            List<double> items = new List<double>();
            for (int i = 1; i <= n; i++)
            {
                Console.Write($"Enter item {i}: ");
                double item = double.Parse(Console.ReadLine());
                items.Add(item);
            }

            double median = CalculateMedian(items);
            double mode = CalculateMode(items);
            double range = CalculateRange(items);
            double firstQuartile = CalculateQuartile(items, 0.25);
            double thirdQuartile = CalculateQuartile(items, 0.75);
            double p90 = CalculatePercentile(items, 0.9);
            double interQuartileRange = thirdQuartile - firstQuartile;
            (double lowerBoundary, double upperBoundary) = CalculateOutlierBoundaries(items, interQuartileRange);
            bool isOutlier = IsOutlier(items, lowerBoundary, upperBoundary);

            Console.WriteLine($"Median: {median}");
            Console.WriteLine($"Mode: {mode}");
            Console.WriteLine($"Range: {range}");
            Console.WriteLine($"First Quartile: {firstQuartile}");
            Console.WriteLine($"Third Quartile: {thirdQuartile}");
            Console.WriteLine($"P90: {p90}");
            Console.WriteLine($"Interquartile Range: {interQuartileRange}");
            Console.WriteLine($"Outlier Boundaries: [{lowerBoundary}, {upperBoundary}]");
            Console.WriteLine($"Is the value an outlier? {isOutlier}");
        }

        static double CalculateMedian(List<double> items)
        {
            items.Sort();
            int count = items.Count;
            if (count % 2 == 0)
            {
                int middle = count / 2;
                return (items[middle - 1] + items[middle]) / 2;
            }
            else
            {
                return items[count / 2];
            }
        }

        static double CalculateMode(List<double> items)
        {
            Dictionary<double, int> frequency = new Dictionary<double, int>();
            foreach (double item in items)
            {
                if (frequency.ContainsKey(item))
                {
                    frequency[item]++;
                }
                else
                {
                    frequency[item] = 1;
                }
            }
            int maxFrequency = frequency.Values.Max();
            return frequency.First(x => x.Value == maxFrequency).Key;
        }

        static double CalculateRange(List<double> items)
        {
            return items.Max() - items.Min();
        }

        static double CalculateQuartile(List<double> items, double percentile)
        {
            items.Sort();
            int count = items.Count;
            double index = (count - 1) * percentile + 1;
            if (index % 1 == 0)
            {
                return items[(int)index - 1];
            }
            else
            {
                int floor = (int)Math.Floor(index);
                int ceiling = (int)Math.Ceiling(index);
                return (items[floor - 1] * (ceiling - index)) + (items[ceiling - 1] * (index - floor));
            }
        }

        static double CalculatePercentile(List<double> items, double percentile)
        {
            items.Sort();
            int count = items.Count;
            double index = (count - 1) * percentile + 1;
            if (index % 1 == 0)
            {
                return items[(int)index - 1];
            }
            else
            {
                int floor = (int)Math.Floor(index);
                int ceiling = (int)Math.Ceiling(index);
                return (items[floor - 1] * (ceiling - index)) + (items[ceiling - 1] * (index - floor));
            }
        }

        static (double, double) CalculateOutlierBoundaries(List<double> items, double interQuartileRange)
        {
            double firstQuartile = CalculateQuartile(items, 0.25);
            double thirdQuartile = CalculateQuartile(items, 0.75);
            double lowerBoundary = firstQuartile - (1.5 * interQuartileRange);
            double upperBoundary = thirdQuartile + (1.5 * interQuartileRange);
            return (lowerBoundary, upperBoundary);
        }

        static bool IsOutlier(List<double> items, double lowerBoundary, double upperBoundary)
        {
            foreach (double item in items)
            {
                if (item < lowerBoundary || item > upperBoundary)
                {
                    return true;
                }
            }
            return false;
        }
    }
}
