# assignment-reactjs
answers


import React, { useState } from 'react';
import axios from 'axios';
import { Bar } from 'react-chartjs-2';
import { saveAs } from 'file-saver';

const App = () => {
  const [wordFrequencies, setWordFrequencies] = useState([]);
  const [chartData, setChartData] = useState(null);

  const fetchWordFrequencies = async () => {
    try {
      const response = await axios.get('https://www.terriblytinytales.com/test.txt');
      const content = response.data;
      const words = content.toLowerCase().match(/\b\w+\b/g);
      const wordMap = {};

      words.forEach((word) => {
        wordMap[word] = (wordMap[word] || 0) + 1;
      });

      const sortedFrequencies = Object.entries(wordMap)
        .sort((a, b) => b[1] - a[1])
        .slice(0, 20);

      setWordFrequencies(sortedFrequencies);
      generateChartData(sortedFrequencies);
    } catch (error) {
      console.log(error);
    }
  };

  const generateChartData = (frequencies) => {
    const labels = frequencies.map((item) => item[0]);
    const data = frequencies.map((item) => item[1]);

    const chartData = {
      labels: labels,
      datasets: [
        {
          label: 'Word Frequency',
          data: data,
          backgroundColor: 'rgba(75,192,192,0.6)',
        },
      ],
    };

    setChartData(chartData);
  };

  const exportToCSV = () => {
    const csvData = wordFrequencies.map((item) => item.join(',')).join('\n');
    const blob = new Blob([csvData], { type: 'text/csv;charset=utf-8' });
    saveAs(blob, 'word_frequencies.csv');
  };

  return (
    <div>
      <button onClick={fetchWordFrequencies}>Submit</button>
      {chartData && (
        <div>
          <Bar data={chartData} />
          <button onClick={exportToCSV}>Export</button>
        </div>
      )}
    </div>
  );
};

export default App;

npm install axios react-chartjs-2 chart.js file-saver
