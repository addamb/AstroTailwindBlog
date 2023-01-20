<script>
    import { onMount } from 'svelte';
    import { parse } from 'rss-to-json';

    const urls = ['https://rss.nytimes.com/services/xml/rss/nyt/HomePage.xml', 'http://rss.cnn.com/rss/cnn_topstories.rss']
    let items = [];
    let rssItems = [];
    onMount(() => {

        //Suffles items 
        function shuffleItems(items) {
            for (let i = items.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [items[i], items[j]] = [items[j], items[i]];
            }
        }


        //loads from rss feeds
        async function load() {
            for (var i = 0; i < urls.length; i++) {
                var rss = await parse(urls[i]);
                if (!rss.hasOwnProperty('message')) {
                    console.log(rss);
                    rss.items.forEach(item => rssItems.push(item));
                } else {
                    console.log(rss[0].message);
                }
                // console.log(rss);
                // rss.items.forEach(item => rssItems.push(item));
            }

            //console.log(rssItems);
            items = rssItems.map(item => {
                //console.log(item.category[0]["$text"]);
                const title = item.title;
                let url = item.link;
                if (!url) {
                    url = item.guid
                }
                const description = item.description;
                const author = item.author;
                const pubDate = new Date(item.published).toLocaleDateString("en-US");
                //console.log(item);

                let categorys = [];
                for (i = 0; i < 2; i++) {
                    if (item.category[i] != undefined) {
                        if (!item.category[i]["$text"].includes('-') && item.category[i]["$text"] != "fnc"){
                            //console.log(item.category[i]["$text"]);
                            categorys.push(item.category[i]["$text"].trim());
                        }
                    }
                }

                // item.category.map(category => {
                //     // if (category !== undefined) {
                //        // console.log(category["$text"]);
                //        if (category.toString().length < 25) {
                //         categorys.push(category["$text"].trim());
                //        }
                //         //categorys.push(category["$text"]);
                //     // }
                // });  
                const image = ("thumbnail" in item.media) ? item.media.thumbnail.url : "";

                return {title, url, description, author, pubDate, categorys, image};

            });

            shuffleItems(items);

            // let items = items
            // .map(value => ({ value, sort: Math.random() }))
            // .sort((a, b) => a.sort - b.sort)
            // .map(({ value }) => value);

            // var rss = await parse('https://moxie.foxnews.com/google-publisher/latest.xml');
            // //console.log(JSON.stringify(rss, null, 4));

            // items = rss.items.map(item => {
            //     console.log(item.media);
            //     const title = item.title;
            //     const url = item.link;
            //     console.log((item));

            //     const image = ("thumbnail" in item.media) ? item.media.thumbnail.url : "";

            //     return {title, url, image};

            // });
	}
    load()
    
  });
</script>

<!-- <h1>New York Times</h1>
<ul>
	{#each items as { title, url }}
		<li>
			<a href={url} target="_blank" rel="noreferrer noopener">
				{title}
			</a>
		</li>
	{/each}
</ul> -->

<!-- {#each items as { title, url, image }}
    <a rel="noopener noreferrer" href={url} class="block max-w-sm gap-3 px-2  mx-auto sm:max-w-full group hover:no-underline focus:no-underline lg:grid lg:grid-cols-12 dark:bg-gray-900">
                <img src={image} alt="" width="151px" height="151px" class="object-cover w-full h-64 rounded sm:h-96 lg:col-span-7 dark:bg-gray-500">
                <div class="p-6 space-y-2 lg:col-span-5 p-6">
                    <h3 class="text-2xl font-semibold sm:text-4xl group-hover:underline group-focus:underline">{title}</h3>
                    <span class="text-xs dark:text-gray-400">February 19, 2021</span>
                    <p>Ei delenit sensibus liberavisse pri. Quod suscipit no nam. Est in graece fuisset, eos affert putent doctus id.</p>
                </div>
            </a>
{/each} -->

<div class="dark:bg-gray-800 dark:text-gray-50">
    
        <!-- <div class="container grid grid-cols-12 mx-auto dark:bg-gray-900">
            <div class="bg-no-repeat bg-cover col-span-full lg:col-span-4" style="background-image: url({image}); background-position: center center; background-blend-mode: multiply; background-size: cover;"></div>
            <div class="flex flex-col p-6 col-span-full row-span-full lg:col-span-8 lg:p-10">
                <div class="flex justify-start space-x-2">
                    {#each categorys as category }
                        <span class="px-2 py-1 text-xs rounded-full dark:bg-violet-400 dark:text-gray-900">{category}</span>
                    {/each}
                </div>
                <h1 class="text-3xl font-semibold">{title}</h1>
                <p class="flex-1 pt-2">{description}</p>
                <a rel="noopener noreferrer" href={url} class="inline-flex items-center pt-2 pb-6 space-x-2 text-sm dark:text-violet-400">
                    <span>Read more</span>
                    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" class="w-4 h-4">
                        <path fill-rule="evenodd" d="M12.293 5.293a1 1 0 011.414 0l4 4a1 1 0 010 1.414l-4 4a1 1 0 01-1.414-1.414L14.586 11H3a1 1 0 110-2h11.586l-2.293-2.293a1 1 0 010-1.414z" clip-rule="evenodd"></path>
                    </svg>
                </a>
                <div class="flex items-center justify-between pt-2">
                   
                    <div class="flex space-x-2">
                        {#if author}
                            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" class="w-5 h-5 dark:text-gray-400">
                                <path fill-rule="evenodd" d="M18 10a8 8 0 11-16 0 8 8 0 0116 0zm-6-3a2 2 0 11-4 0 2 2 0 014 0zm-2 4a5 5 0 00-4.546 2.916A5.986 5.986 0 0010 16a5.986 5.986 0 004.546-2.084A5 5 0 0010 11z" clip-rule="evenodd"></path>
                            </svg>
                            <span class="self-center text-sm">by {author}</span>
                        {/if}
                    </div>
                    <span class="text-xs">Published: {pubDate}</span>
                </div>
            </div>
        </div> -->

        <div class="grid justify-center grid-cols-1 gap-6 sm:grid-cols-2 lg:grid-cols-3">
            {#each items as { title, url, description, author, pubDate, categorys, image }}
        {#if image}
			<a rel="noopener noreferrer" href="{url}" class="max-w-sm mx-auto group hover:no-underline focus:no-underline dark:bg-gray-900">
				<img role="presentation" class="object-cover w-full rounded h-44 dark:bg-gray-500" src={image}>
				<div class="p-6 space-y-2">
					<h3 class="text-2xl font-semibold group-hover:underline group-focus:underline">{title}</h3>
					<span class="text-xs dark:text-gray-400">{pubDate}</span>
					<p>{description}</p>
				</div>
			</a>
			<!-- <a rel="noopener noreferrer" href="#" class="max-w-sm mx-auto group hover:no-underline focus:no-underline dark:bg-gray-900">
				<img role="presentation" class="object-cover w-full rounded h-44 dark:bg-gray-500" src="https://source.unsplash.com/random/480x360?2">
				<div class="p-6 space-y-2">
					<h3 class="text-2xl font-semibold group-hover:underline group-focus:underline">In usu laoreet repudiare legendos</h3>
					<span class="text-xs dark:text-gray-400">January 22, 2021</span>
					<p>Mei ex aliquid eleifend forensibus, quo ad dicta apeirian neglegentur, ex has tantas percipit perfecto. At per tempor albucius perfecto, ei probatus consulatu patrioque mea, ei vocent delicata indoctum pri.</p>
				</div>
			</a> -->
			<!-- <a rel="noopener noreferrer" href="#" class="max-w-sm mx-auto group hover:no-underline focus:no-underline dark:bg-gray-900">
				<img role="presentation" class="object-cover w-full rounded h-44 dark:bg-gray-500" src="https://source.unsplash.com/random/480x360?3">
				<div class="p-6 space-y-2">
					<h3 class="text-2xl font-semibold group-hover:underline group-focus:underline">In usu laoreet repudiare legendos</h3>
					<span class="text-xs dark:text-gray-400">January 23, 2021</span>
					<p>Mei ex aliquid eleifend forensibus, quo ad dicta apeirian neglegentur, ex has tantas percipit perfecto. At per tempor albucius perfecto, ei probatus consulatu patrioque mea, ei vocent delicata indoctum pri.</p>
				</div>
			</a>
			<a rel="noopener noreferrer" href="#" class="max-w-sm mx-auto group hover:no-underline focus:no-underline dark:bg-gray-900 hidden sm:block">
				<img role="presentation" class="object-cover w-full rounded h-44 dark:bg-gray-500" src="https://source.unsplash.com/random/480x360?4">
				<div class="p-6 space-y-2">
					<h3 class="text-2xl font-semibold group-hover:underline group-focus:underline">In usu laoreet repudiare legendos</h3>
					<span class="text-xs dark:text-gray-400">January 24, 2021</span>
					<p>Mei ex aliquid eleifend forensibus, quo ad dicta apeirian neglegentur, ex has tantas percipit perfecto. At per tempor albucius perfecto, ei probatus consulatu patrioque mea, ei vocent delicata indoctum pri.</p>
				</div>
			</a>
			<a rel="noopener noreferrer" href="#" class="max-w-sm mx-auto group hover:no-underline focus:no-underline dark:bg-gray-900 hidden sm:block">
				<img role="presentation" class="object-cover w-full rounded h-44 dark:bg-gray-500" src="https://source.unsplash.com/random/480x360?5">
				<div class="p-6 space-y-2">
					<h3 class="text-2xl font-semibold group-hover:underline group-focus:underline">In usu laoreet repudiare legendos</h3>
					<span class="text-xs dark:text-gray-400">January 25, 2021</span>
					<p>Mei ex aliquid eleifend forensibus, quo ad dicta apeirian neglegentur, ex has tantas percipit perfecto. At per tempor albucius perfecto, ei probatus consulatu patrioque mea, ei vocent delicata indoctum pri.</p>
				</div>
			</a>
			<a rel="noopener noreferrer" href="#" class="max-w-sm mx-auto group hover:no-underline focus:no-underline dark:bg-gray-900 hidden sm:block">
				<img role="presentation" class="object-cover w-full rounded h-44 dark:bg-gray-500" src="https://source.unsplash.com/random/480x360?6">
				<div class="p-6 space-y-2">
					<h3 class="text-2xl font-semibold group-hover:underline group-focus:underline">In usu laoreet repudiare legendos</h3>
					<span class="text-xs dark:text-gray-400">January 26, 2021</span>
					<p>Mei ex aliquid eleifend forensibus, quo ad dicta apeirian neglegentur, ex has tantas percipit perfecto. At per tempor albucius perfecto, ei probatus consulatu patrioque mea, ei vocent delicata indoctum pri.</p>
				</div>
			</a> -->
		
        {/if}
        <!-- <br /> -->
    {/each}
</div>
    
</div>