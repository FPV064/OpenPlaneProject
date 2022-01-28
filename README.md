<p align="center">
  <img src="4.jpg" height="250" title="hover text">
  <img src="43.jpg" height="250" alt="accessibility text">
</p>

# OpenPlaneProject
This project is all about designing and building affordable autonomous planes. It aims to provide plans, files and detailed info about the build for free. While the open source software solutions for controlling UAVs are out there and easy to use, this project focuses on the mechanical part to get everyone a reliable platform for many use cases. You can find more information on Hackaday.io: https://hackaday.io/project/183150-open-plane-project

# Endurance flight video:
<a  href="http://www.youtube.com/watch?feature=player_embedded&v=-ks4JAQizB8
" target="_blank"><img src="http://img.youtube.com/vi/-ks4JAQizB8/0.jpg"
alt="Endurance flight" width="480" height="330" border="0" />
</a>

# Performance calculator:
<form id="calculations-form" name="calculations" action="javascript:void(0);">
            <label for="units">Units:</label>
            <select name="units" id="units">
                <option value="metric">Metric</option>
                <option value="us">US Customary</option>
            </select>
            <h3>Constant Parameters</h3>
            <div>
                <label for="avg-power">Average Power</label>
                <input name="avg-power" id="avg-power" pattern="[0-9.]+" type="html" value="30">
                <span class="unit">W</span>
            </div>
            <div>
                <label for="avg-speed">Average Speed</label>
                <input name="avg-speed" id="avg-speed" pattern="[0-9.]+" type="html" value="43.92">
                <span class="unit">km/h</span>
            </div>
            <div>
                <label for="weight">Weight</label>
                <input name="weight" id="weight" pattern="[0-9.]+" type="html" value="0.65">
                <span class="unit">kg</span>
            </div>
            <div>
                <label for="air-density">Air Density</label>
                <input name="air-density" id="air-density" pattern="[0-9.]+" type="html" value="1.225">
                <span class="unit">kg/m<sup>3</sup></span>
            </div>
            <br/>
            <h3>Custom Parameters</h3>
            <div>
                <label for="plane-weight">Empty Plane Weight</label>
                <input name="plane-weight" id="plane-weight" pattern="[0-9.]+" type="html" required>
                <span class="unit">kg</span>
            </div>
            <div>
                <label for="battery-capacity">Battery Capacity</label>
                <input name="battery-capacity" id="battery-capacity" pattern="[0-9.]+" type="html" required>
                <span class="unit">Wh</span>
            </div>
            <div>
                <label for="battery-weight">Battery Weight</label>
                <input name="battery-weight" id="battery-weight" pattern="[0-9.]+" type="html" required>
                <span class="unit">kg</span>
            </div>
            <div>
                <label for="camera-drain">Camera Drain</label>
                <input name="camera-drain" id="camera-drain" pattern="[0-9.]+" type="html" required>
                <span class="unit">W</span>
            </div>
            <br/>
            <input id="calculate-button" type="submit" value="Calculate">
            <br/>
            <div id="calculation-results" style="display:none;">
                <span>Flight Time: <span id="flight-time"></span> <span class="unit">minutes</span>.</span>
                <span>Speed: <span id="speed"></span> <span class="unit">km/h</span>.</span>
                <span>Distance: <span id="distance"></span> <span class="unit">km</span>.</span>
            </div>
        </form>
        <script src="https://code.jquery.com/jquery-3.6.0.slim.js" integrity="sha256-HwWONEZrpuoh951cQD1ov2HUK5zA5DwJ1DNUXaM6FsY=" crossorigin="anonymous"></script>
        <script>
            var constantValues = [30, 43.92, 0.65, 1.225]; //Average Power (W), Average Speed (km/h), Weight (kg), Air Density (kg/m3)

            var metric = ["kg", "km/h", "kg/m<sup>3</sup>", "km"];
            var usc = ["lb", "mph", "lb/ft<sup>3</sup>", "mi"];

            function replaceUnits() {
                var test = [$('#plane-weight'), $('#battery-weight')];
                var resultValues = [$('#speed'), $('#distance')];

                if ($('#units').val() == 'us') {
                    $('.unit').each(function() {
                        if (metric.includes($(this).html())) {
                            $(this).html(usc[metric.indexOf($(this).html())]);
                        }
                    });

                    $('#avg-speed').val(27.29);
                    $('#weight').val(1.43);
                    $('#air-density').val(0.0765);

                    for (let x of test) {
                        if (!isNaN(x.val()) && x.val() != "") {
                            x.val(Number((x.val() * 2.205).toFixed(3)));
                        }
                    }

                    for (let i of resultValues) {
                        console.log(i.text())
                        if (!isNaN(i.text()) && i.text() != "") {
                            i.text(Number((i.text() / 1.609).toFixed(3)));
                        }
                    }
                }
                else if ($('#units').val() == 'metric') {
                    $('.unit').each(function() {
                        if (usc.includes($(this).html())) {
                            $(this).html(metric[usc.indexOf($(this).html())]);
                        }
                    });

                    $('#avg-speed').val(43.92);
                    $('#weight').val(0.65);
                    $('#air-density').val(1.225);

                    for (let x of test) {
                        if (!isNaN(x.val()) && x.val() != "") {
                            x.val(Number((x.val() / 2.205).toFixed(3)));
                        }
                    }

                    for (let i of resultValues) {
                        console.log(i.text())
                        if (!isNaN(i.text()) && i.text() != "") {
                            i.text(Number((i.text() * 1.609).toFixed(3)));
                        }
                    }
                }
            }

            function calculate() {
                //TODO: check for empty fields and NaNs
                
                //Get values
                
                var avgPower = Number($('#avg-power').val());
                var avgSpeed = Number($('#avg-speed').val());
                var weight = Number($('#weight').val());
                var airDensity = Number($('#air-density').val());

                var planeWeight = Number($('#plane-weight').val());
                var capacity = Number($('#battery-capacity').val());
                var batteryWeight = Number($('#battery-weight').val());
                var drain = Number($('#camera-drain').val());

                //Convert units to metric

                if ($('#units').val() == 'us') {
                    avgSpeed *= 1.609;
                    weight /= 2.205;
                    airDensity *= 16.018;
                    planeWeight /= 2.205;
                    batteryWeight /= 2.205;
                }

                //Big math

                var totalWeight = planeWeight + batteryWeight;
                avgSpeed /= 3.6;

                var liftCoefficient = (2 * weight * 9.81) / (airDensity * Math.pow(avgSpeed, 2));
                var dragCoefficient = (2 * (avgPower / avgSpeed)) / (airDensity * Math.pow(avgSpeed, 2));

                //console.log(liftCoefficient);
                //console.log(dragCoefficient);

                var flightTime = capacity / (0.5 * airDensity * Math.pow(Math.sqrt(2 * 9.81 * totalWeight / (liftCoefficient * airDensity)), 3) * dragCoefficient + drain) * 60;
                var speed = Math.sqrt((2 * totalWeight * 9.81) / (airDensity * liftCoefficient)) * 3.6;
                var distance = speed * (flightTime / 60);
                
                //Convert back to USC if needed

                if ($('#units').val() == 'us') {
                    console.log(speed, distance)
                    speed /= 1.609;
                    distance /= 1.609;
                }

                //console.log(flightTime);
                //console.log(speed);
                //console.log(distance);

                $('#flight-time').text(flightTime.toFixed(3));
                $('#speed').text(speed.toFixed(3));
                $('#distance').text(distance.toFixed(3));
                $('#calculation-results').show();

                return false;
            }

            $(document).ready(function() {
                $('#units').change(function() {
                    replaceUnits();
                });
                
                $('#calculations-form').on('submit', function() {
                    calculate();
                });
            });
        </script>
        <style>
            #calculation-results {
                margin-top: 1rem;
            }
            #calculation-results > span {
                display: block;
            }
        </style>

