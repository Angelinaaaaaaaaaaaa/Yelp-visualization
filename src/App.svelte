<script>
    import { onMount } from 'svelte';
    import * as d3 from 'd3';
    import mapboxgl from 'mapbox-gl';

    mapboxgl.accessToken = "pk.eyJ1IjoiYW5nZWxpbmFhYWFhYWEiLCJhIjoiY2x3NGV1OWduMTR2cjJucnNqMGF5OThzYSJ9.NxDqSdB_jZHQDikpDJOMXw";
    const map = new mapboxgl.Map({
        container: "map",
        style: "mapbox://styles/mapbox/dark-v10",
        center: [-119.6982, 34.4208],
        zoom: 12,
        minZoom: 10,
        maxZoom: 18,
    });

    let markers;
    let allData = []; // Store all data here
    let slider_star = 0;
    let slider_label = "All Business";

    // Define slider_label as reactive variable
    $: slider_label = slider_star === 0 ? "All Business" : `${slider_star} star${slider_star > 1 ? 's' : ''}`;

    async function fetchData() {
        try {
            const response = await fetch("yelp_academic_dataset_business.json");
            const data = await response.json();
            allData = data; // Store the fetched data
            filterMarkers(slider_star); // Initialize markers based on the current slider value
        } catch (error) {
            console.error('Error fetching JSON data:', error);
        }
    }

    function createStationMarkers(data) {
        // Remove existing markers
        d3.select(map.getCanvasContainer()).selectAll("svg").remove();

        markers = d3.select(map.getCanvasContainer())
            .append("svg")
            .attr("width", "100%")
            .attr("height", "100%")
            .style("position", "absolute")
            .style("z-index", 2)
            .selectAll("circle")
            .data(data)
            .enter()
            .append("circle")
            .attr("r", d => scaleRadiusNumberOfReviews(d.review_count)) // Adjust the radius based on review_count
            .style("fill", d => mapStarsToColor(d.stars))
            .attr("stroke", "#808080")
            .attr("stroke-width", 1)
            .attr("fill-opacity", 0.4)
            .attr("name", d => d.name)
            .on("mouseover", handleMouseOver)
            .on("mouseout", handleMouseOut);

        positionStationMarkers();
    }

    function positionStationMarkers() {
        markers.attr("cx", d => project(d).x)
            .attr("cy", d => project(d).y);
    }

    function mapStarsToColor(stars) {
        if (stars > 4) {
            return "#FF0000"; // Red
        } else if (stars > 3 && stars <= 4) {
            return "#FFA500"; // Orange
        } else if (stars > 2 && stars <= 3) {
            return "#FFD700"; // Yellow
        } else if (stars > 1 && stars <= 2) {
            return "#FFC300"; // Mustard
        } else {
            return "#FFFFFF"; // White
        }
    }

    function scaleRadiusNumberOfReviews(reviewCount) {
        // Scale the radius based on the review count
        return Math.log(reviewCount) * 3; // Adjust the factor as needed
    }

    function project(d) {
        return map.project(new mapboxgl.LngLat(+d.longitude, +d.latitude));
    }

    function filterMarkers(slider_star) {
        // Filter data based on the slider value
        let filteredData;
        if (slider_star === 0) {
            filteredData = allData.filter(item => item.city === "Santa Barbara");
        } else {
            filteredData = allData.filter(item => item.city === "Santa Barbara" && item.stars > (slider_star - 1) && item.stars <= slider_star);
        }

        // Update the markers based on the filtered data
        createStationMarkers(filteredData);
    }

    // Use onMount to fetch data and initialize markers
    onMount(() => {
        fetchData();
    });

    // React to changes in slider_star
    $: {
        if (allData.length !== 0) {
            filterMarkers(slider_star);
        }
    }

    map.on('move', positionStationMarkers);
    map.on('moveend', positionStationMarkers);

    // Handle mouse over event to show tooltip
    function handleMouseOver(event, d) {
		const text = `${d.name}<br>(${d.categories})`;
        const tooltip = d3.select(map.getCanvasContainer()).append("div")
            .attr("class", "tooltip")
            .style("position", "absolute")
            .style("background", "#fff")
            .style("padding", "5px")
            .style("border-radius", "3px")
            .style("pointer-events", "none")
            .style("z-index", 10)
            .style("left", `${event.pageX + 5}px`)
            .style("top", `${event.pageY - 28}px`)
            .text(text)

        d3.select(this).attr("fill-opacity", 1);
    }

    // Handle mouse out event to hide tooltip
    function handleMouseOut(event, d) {
        d3.select(map.getCanvasContainer()).select(".tooltip").remove();
        d3.select(this).attr("fill-opacity", 0.4);
    }
</script>

<main>
    <div id="map" style="position: absolute; top: 0; bottom: 0; width: 100%;"></div>
    <div class="overlay">
        <label>{slider_label}</label>
        <input
            id="slider"
            type="range"
            min="0"
            max="5"
            bind:value={slider_star}
        />
    </div>
</main>

<style>
    .tooltip {
        position: absolute;
        text-align: center;
        width: auto;
        height: auto;
        padding: 2px;
        font: 12px sans-serif;
        background: lightsteelblue;
        border: 0px;
        border-radius: 8px;
        pointer-events: none;
    }
</style>

	