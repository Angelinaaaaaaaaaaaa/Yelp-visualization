<script>
    import { onMount } from 'svelte';
    import { writable } from 'svelte/store';
    import * as d3 from 'd3';
    import mapboxgl from 'mapbox-gl';

    mapboxgl.accessToken = "pk.eyJ1IjoiYW5nZWxpbmFhYWFhYWEiLCJhIjoiY2x3NGV1OWduMTR2cjJucnNqMGF5OThzYSJ9.NxDqSdB_jZHQDikpDJOMXw";
    const map = new mapboxgl.Map({
        container: "map",
        style: "mapbox://styles/mapbox/light-v11",
        center: [-119.6982, 34.4208],
        zoom: 12,
        minZoom: 10,
        maxZoom: 18,
    });

    let markers;
    let allData = [];
    let slider_star = 0;
    let slider_reviews = 0;
    let slider_label = "All Business";
    let searchQuery = writable('');
    let dropdownList = writable([]);

    $: slider_label = slider_star === 0 ? "All Business" : `${slider_star} star${slider_star > 1 ? 's' : ''}`;

    async function fetchData() {
        try {
            const response = await fetch("yelp_academic_dataset_business.json");
            const data = await response.json();
            allData = data;
            filterMarkers(slider_star, slider_reviews, $searchQuery);
        } catch (error) {
            console.error('Error fetching JSON data:', error);
        }
    }

    function createStationMarkers(data) {
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
            .attr("r", d => scaleRadiusNumberOfReviews(d.review_count))
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
            return "#006400";
        } else if (stars > 3 && stars <= 4) {
            return "#FFDB58";
        } else if (stars > 2 && stars <= 3) {
            return "#FFA500";
        } else if (stars > 1 && stars <= 2) {
            return "#FF8C00";
        } else {
            return "#FF0000";
        }
    }

    function scaleRadiusNumberOfReviews(reviewCount) {
        return Math.log(reviewCount) * 3;
    }

    function project(d) {
        return map.project(new mapboxgl.LngLat(+d.longitude, +d.latitude));
    }

    function filterMarkers(slider_star, slider_reviews, searchQuery) {
        let filteredData;
        if (slider_star === 0 && slider_reviews === 0) {
            filteredData = allData.filter(item => item.city === "Santa Barbara" && item.name.toLowerCase().includes(searchQuery.toLowerCase()));
        } else if (slider_star === 0) {
            filteredData = allData.filter(item => item.city === "Santa Barbara" && item.review_count >= slider_reviews && item.name.toLowerCase().includes(searchQuery.toLowerCase()));
        } else if (slider_reviews === 0) {
            filteredData = allData.filter(item => item.city === "Santa Barbara" && item.stars > (slider_star - 1) && item.stars <= slider_star && item.name.toLowerCase().includes(searchQuery.toLowerCase()));
        } else {
            filteredData = allData.filter(item => item.city === "Santa Barbara" && item.stars > (slider_star - 1) && item.stars <= slider_star && item.review_count >= slider_reviews && item.name.toLowerCase().includes(searchQuery.toLowerCase()));
        }

        createStationMarkers(filteredData);

        // Update dropdown list with top five businesses by review count
        dropdownList.set(filteredData
            .sort((a, b) => b.review_count - a.review_count)
            .slice(0, 5));
    }

    onMount(() => {
        fetchData();
    });

    $: {
        if (allData.length !== 0) {
            filterMarkers(slider_star, slider_reviews, $searchQuery);
        }
    }

    map.on('move', positionStationMarkers);
    map.on('moveend', positionStationMarkers);

    function handleMouseOver(event, d) {
        const text = `<strong>${d.name}</strong><br>Stars: ${d.stars}<br>Reviews: ${d.review_count}<br>${d.categories}`;
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
            .html(text);

        d3.select(this)
            .attr("fill-opacity", 1)
            .attr("stroke", "#000000")
            .attr("stroke-width", 2);
    }

    function handleMouseOut(event, d) {
        d3.select(map.getCanvasContainer()).select(".tooltip").remove();
        d3.select(this)
            .attr("fill-opacity", 0.4)
            .attr("stroke", "#808080")
            .attr("stroke-width", 1);
    }

    function handleDropdownClick(item) {
        searchQuery.set(item.name);
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
        <label>Minimum Reviews: {slider_reviews}</label>
        <input
            id="review-slider"
            type="range"
            min="0"
            max="1000"
            bind:value={slider_reviews}
        />
        <input
            class="search-box"
            type="text"
            placeholder="Search by name"
            bind:value={$searchQuery}
        />
        {#if $searchQuery}
            <ul class="dropdown">
                {#each $dropdownList as item}
                    <li on:click={() => handleDropdownClick(item)}>{item.name} ({item.review_count} reviews)</li>
                {/each}
            </ul>
        {/if}
    </div>
</main>
