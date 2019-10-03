var express = require('express');
var router = express.Router();
var path = require('path');
var Chart = require('chart.js');
const jsdom = require("jsdom");
const { JSDOM } = jsdom;
const { createCanvas, loadImage } = require('canvas')
var fabric = require('fabric').fabric;

const { CanvasRenderService } = require('chartjs-node-canvas');

/* GET home page. */
router.get('/', function (req, res, next) {
  //res.render('index', { title: 'Express' });
  res.sendFile(path.resolve('public/chart.html'));
});

router.get('/chart', function (req, res, next) {

  const canvas = new fabric.Canvas('c');
  canvas.setHeight(200);
  canvas.setWidth(200);
  const ctx = canvas.getContext('2d')

  // Write "Awesome!"
  ctx.font = '30px Impact'
  ctx.rotate(0.1)
  ctx.fillText('Awesome!', 50, 100)

  // Draw line under text
  var text = ctx.measureText('Awesome!')
  ctx.strokeStyle = 'rgba(0,0,0,0.5)'
  ctx.beginPath()
  ctx.lineTo(50, 102)
  ctx.lineTo(50 + text.width, 102)
  ctx.stroke()

  res.send({ response: canvas.toDataURL() });
});

router.get('/chart2', function (req, res, next) {
  res.send({ response: "Correcto!" });
});

router.get('/chart3', function (req, res, next) {
  (async () => {
    const width = 400; //px
    const height = 400; //px
    const configuration = {
      type: 'bar',
      data: {
        labels: ['Red', 'Blue', 'Yellow', 'Green', 'Purple', 'Orange'],
        datasets: [{
          label: '# of Votes',
          data: [12, 19, 3, 5, 2, 3],
          backgroundColor: [
            'rgba(255, 99, 132, 0.2)',
            'rgba(54, 162, 235, 0.2)',
            'rgba(255, 206, 86, 0.2)',
            'rgba(75, 192, 192, 0.2)',
            'rgba(153, 102, 255, 0.2)',
            'rgba(255, 159, 64, 0.2)'
          ],
          borderColor: [
            'rgba(255, 99, 132, 1)',
            'rgba(54, 162, 235, 1)',
            'rgba(255, 206, 86, 1)',
            'rgba(75, 192, 192, 1)',
            'rgba(153, 102, 255, 1)',
            'rgba(255, 159, 64, 1)'
          ],
          borderWidth: 1
        }]
      },
      options: {
        scales: {
          yAxes: [{
            ticks: {
              beginAtZero: true
            }
          }]
        }
      }
    };
    const canvasRenderService = new CanvasRenderService(width, height);
    const dataUrl = await canvasRenderService.renderToDataURL(configuration);

    res.send({ base64Chart: dataUrl });

  })();
});

router.get('/chart4', function (req, res, next) {
  (async () => {
    const width = 400; //px
    const height = 400; //px
    const configuration = {
      type: 'bar',
      data: {
        labels: ['Red', 'Blue', 'Yellow', 'Green', 'Purple', 'Orange'],
        datasets: [{
          label: '# of Votes',
          data: [12, 19, 3, 5, 2, 3],
          backgroundColor: [
            'rgba(255, 99, 132, 0.2)',
            'rgba(54, 162, 235, 0.2)',
            'rgba(255, 206, 86, 0.2)',
            'rgba(75, 192, 192, 0.2)',
            'rgba(153, 102, 255, 0.2)',
            'rgba(255, 159, 64, 0.2)'
          ],
          borderColor: [
            'rgba(255, 99, 132, 1)',
            'rgba(54, 162, 235, 1)',
            'rgba(255, 206, 86, 1)',
            'rgba(75, 192, 192, 1)',
            'rgba(153, 102, 255, 1)',
            'rgba(255, 159, 64, 1)'
          ],
          borderWidth: 1
        }]
      },
      options: {
        scales: {
          yAxes: [{
            ticks: {
              beginAtZero: true
            }
          }]
        }
      }
    };

    const dataUrl = await renderToDataURL(configuration, width, height);
    res.send({ base64Chart: dataUrl });

  })();
});

function renderChart(configuration, width, height) {
  let domstr = "<!DOCTYPE html><body><div id=\"div1\"><canvas id=\"canvas\" style=\"width:" + width + ";height:" + height + ";\"</div></body>";
  const dom = new JSDOM(domstr);
  const canvas = dom.window.document.getElementById("canvas");
  canvas.height = height;
  canvas.width = width;
  canvas.style = {};
  // Disable animation (otherwise charts will throw exceptions)
  configuration.options = configuration.options || {};
  configuration.options.responsive = false;
  configuration.options.animation = false;
  const context = canvas.getContext('2d');
  return new Chart(context, configuration);
}

function renderToDataURL(configuration, width, height, mimeType = 'image/png') {
  const chart = renderChart(configuration, width, height);
  return new Promise((resolve, reject) => {
    const canvas = chart.canvas;
    canvas.toDataURL(mimeType, (error, png) => {
      chart.destroy();
      if (error) {
        return reject(error);
      }
      return resolve(png);
    });
  });
}

router.post("/chartPost", function (req, res) {
  let config = req.body;

  (async () => {
    const width = 450; //px
    const height = 225; //px
    const configuration = config;
    const canvasRenderService = new CanvasRenderService(width, height, (ChartJS) => { });
    const image = await canvasRenderService.renderToBuffer(configuration);
    const dataUrl = await canvasRenderService.renderToDataURL(configuration);
    let stream = canvasRenderService.renderToStream(configuration);

    res.send({ base64Chart: dataUrl });
  })();

  //res.send({ object : x} );
});

module.exports = router;
