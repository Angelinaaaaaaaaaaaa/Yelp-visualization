<script>
	mapboxgl.accessToken = "pk.eyJ1IjoiYW5nZWxpbmFhYWFhYWEiLCJhIjoiY2x3NGV1OWduMTR2cjJucnNqMGF5OThzYSJ9.NxDqSdB_jZHQDikpDJOMXw";
	const map = new mapboxgl.Map({
		container: "map",
		style: "mapbox://styles/mapbox/navigation-day-v1", 
		center: [-74.005974, 40.712776],
		// center: [36.778259, -119.417931] CA
		zoom: 12,
		minZoom: 10,
		maxZoom: 10000,
	});

	map.on("load", () => {
    fetch("yelp_academic_dataset_business.json")
        .then(response => response.text())
        .then(data => {
            const lines = data.trim().split('\n');
            
            lines.forEach(line => {
                const business = JSON.parse(line);
                
                if (business.state === 'NY') {
                    map.addLayer({
                        id: business.business_id,
                        type: "circle",
                        source: {
                            type: "geojson",
                            data: {
                                type: "Feature",
                                geometry: {
                                    type: "Point",
                                    coordinates: [business.longitude, business.latitude]
                                },
                                properties: {
                                    name: business.name,
                                    stars: business.stars
                                }
                            }
                        },
                        paint: {
                            "circle-color": "#000000",
                            "circle-radius": 5
                        }
                    });
                }
            });
        })
        .catch(error => console.error('Error fetching or parsing data:', error));
	});

	map.on("load", () => {
		fetch("yelp_academic_dataset_business.json")
			.then(response => response.text())
			.then(data => {
				const lines = data.trim().split('\n');

				lines.forEach(line => {
					const business = JSON.parse(line);

					map.addLayer({
						id: business.business_id,
						type: "circle",
						source: {
							type: "geojson",
							data: {
								type: "Feature",
								geometry: {
									type: "Point",
									coordinates: [business.longitude, business.latitude]
								},
								properties: {
									name: business.name,
									stars: business.stars
								}
							}
						},
						paint: {
							"circle-color": "#000000",
							"circle-radius": 500
						}
					});
				});
			})
		.catch(error => console.error('Error fetching or parsing data:', error));
	});



    // map.on("viewreset", position_station_markers);
	// map.on("move", position_station_markers);
	// map.on("moveend", position_station_markers);


//     function create_station_markers(station_data) {
// 		station_markers = marker_container
// 			.selectAll("circle")
// 			.data(station_data)
// 			.enter()
// 			.append("circle")
// 			.attr("r", 5)
// 			.style("fill", "#808080")
// 			.attr("stroke", "#808080")
// 			.attr("stroke-width", 1)
// 			.attr("fill-opacity", 0.4)
// 			.attr("name", function (d) {
// 				return d["name"];
// 			});

// 		position_station_markers();
// 	}

// 	function position_station_markers() {
// 		station_markers
// 			.attr("cx", function (d) {
// 				return project(d).x;
// 			})
// 			.attr("cy", function (d) {
// 				return project(d).y;
// 			});
// 	}
// 	function project(d) {
// 		return map.project(new mapboxgl.LngLat(+d.lon, +d.lat));
// 	}

//     function update_station_markers() {
//     station_markers
//         .transition()
//         .duration(1000)
//         .attr("r", function (d) {
//             let trafficVolume =
//                 arrivals[d["station_id"]] + departures[d["station_id"]];
//             return scaleRadiusTrafficVolume(trafficVolume);
//         })
//         .style("fill", function (d) {
//             let arrivalRatio = arrivals[d["station_id"]] / (arrivals[d["station_id"]] + departures[d["station_id"]]);
//             return interpolateColor(arrivalRatio);
//         });
//     }

//     function interpolateColor(ratio) {
//         const colorScale = d3.scaleLinear()
//             .domain([0, 1])
//             .range(["green", "purple"]); 
//         return colorScale(ratio);
//     }

// 	function scaleRadiusTrafficVolume(traffic, maxTraffic = 500) {
// 		const scaleRadius = d3.scaleSqrt()
// 			.domain([0, 1, maxTraffic])
// 			.range([0, 3, 20]);
// 		return scaleRadius(traffic);
// 	}
//     function tallyTrips(trips_data) {
// 		arrivals.fill(0);
// 		departures.fill(0);
// 		for (let i = 0; i < trips_data.length; ++i) {
// 			arrivals[trips_data[i]["end station id"]]++;
// 			departures[trips_data[i]["start station id"]]++;
// 		}
// 	}
    
//     function filterTrips(sliderTime) {
// 		let value = sliderTimeScale(sliderTime);
// 		let filterWindowHours = 0;
// 		let filterWindowMinutes = 120;
// 		let filterHours = value.getHours();
// 		let filterMinutes = value.getMinutes();
// 		return trip_data.filter(function (trip) {
// 			let tripStartTime = new Date(trip["starttime"]);
// 			let tripEndTime = new Date(trip["stoptime"]);
// 			let filterStartTime = new Date(tripStartTime.getTime());
// 			let filterEndTime = new Date(tripEndTime.getTime());
// 			filterStartTime.setHours(filterHours - filterWindowHours / 2);
// 			filterEndTime.setHours(filterHours + filterWindowHours / 2);
// 			filterStartTime.setMinutes(filterMinutes - filterWindowMinutes / 2);
// 			filterEndTime.setMinutes(filterMinutes + filterWindowMinutes / 2);
// 			return (
// 				(tripStartTime >= filterStartTime &&
// 					tripStartTime <= filterEndTime) ||
// 				(tripEndTime >= filterStartTime && tripEndTime <= filterEndTime)
// 			);
// 		});
// 	}
// 	const sliderTimeScale = d3
// 		.scaleTime()
// 		.domain([0, 1440])
// 		.range([new Date("2015-12-01 00:00"), new Date("2015-12-02 00:00")]);

//     let stationsFile = "https://raw.githubusercontent.com/dsc-courses/dsc106-wi24/gh-pages/resources/data/lab6_station_info.json";
// 	let tripFile = "https://raw.githubusercontent.com/dsc-courses/dsc106-wi24/gh-pages/resources/data/lab6_bluebikes_2020.csv";
// 	let station_data = [];
// 	let station_markers;
// 	let trip_data = [];
//     let arrivals = new Array(600).fill(0);
// 	let departures = new Array(600).fill(0);
//     let filtered_trip_data = [];
//     let slider_time = 720;
// 	let slider_label = "";

	const marker_container = d3
		.select(map.getCanvasContainer() )
		.append("svg")
		.attr("width", "100%")
		.attr("height", "100%")
		.style("position", "absolute")
		.style("z-index", 2);
	
	let businessesFile = "yelp_academic_dataset_business.json";
	let business_data = [];
	let business_markers;

	fetch(businessesFile)
		.then((response) => response.text()) // Read response as text
		.then((data) => {
			// Split the text into an array of lines
			const lines = data.trim().split('\n');
			
			// Process each line individually
			lines.forEach((line) => {
				const business = JSON.parse(line); // Parse JSON from each line
				
				// Create a marker for each business
				const marker = L.circleMarker([business.latitude, business.longitude], {
					radius: 5,
					fillColor: "#000000", // Black circle
					color: "#808080",
					weight: 1,
					opacity: 0.4,
					fillOpacity: 0.4
				}).bindPopup(business.name); // Add popup with business name
				
				// Add the marker to the business_markers layer group
				business_markers.addLayer(marker);
			});
		})
		.catch((error) => console.error('Error fetching or parsing data:', error));

	// Initialize Leaflet map
	const map = L.map('map').setView([40, -100], 4);

	// Add OpenStreetMap tile layer
	L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
		attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
	}).addTo(map);

	// Create layer group for business markers
	business_markers = L.layerGroup().addTo(map);


// 	fetch(stationsFile)
// 		.then((response) => response.json())
// 		.then((d) => (station_data = d.data.stations))
// 		.then((d) => create_station_markers(d));

// 	d3.csv(tripFile).then(function (d) {
// 		trip_data = d;
// 		console.log(trip_data);
// 	});

//     $: {
// 	if (trip_data.length !== 0) {
// 			filtered_trip_data = filterTrips(slider_time);
// 			tallyTrips(filtered_trip_data);
// 			update_station_markers();
// 		}
// 	slider_label = sliderTimeScale(slider_time).toLocaleTimeString([], {
// 		hour: "numeric",
// 		minute: "2-digit",
// 	});
// }

</script>

<main>
    <!-- <div class="overlay">
		<label>{slider_label}</label>
		<input
			id="slider"
			type="range"
			min="1"
			max="1440"
			bind:value={slider_time}
		/>
	</div> -->

</main>
