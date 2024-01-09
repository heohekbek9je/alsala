<script>

  // life cycle
  onMount(()=>{
    get_countries()
    const interval = setInterval(() => {
			time = new Date().toLocaleTimeString('en-US',mawaqeet_data?.timezone && {timeZone: mawaqeet_data?.timezone});
		}, 1000);

    // detect keyboard open in mobile to avoid design issues
    window.addEventListener("resize", function() {
      if(window.innerHeight == fullWindowHeight) {
        show_developed_by = true;
      } else if(window.innerHeight < fullWindowHeight*0.9) {
        show_developed_by = false;
      }
    });

		return () => {
			clearInterval(interval);
      window.removeEventListener("resize", function() {
        if(window.innerHeight == fullWindowHeight) {
          show_developed_by = true;
        } else if(window.innerHeight < fullWindowHeight*0.9) {
          show_developed_by = false;
        }
      });
		};
  })
</script>

<!-- BG ovrelay -->
<div class="absolute backdrop-blur-[3px] inset-0 w-full h-screen z-0 bg-black bg-opacity-40"/>

<main class="absolute inset-0 w-full h-screen z-50 flex items-center justify-center px-8">  
  <div class="w-96 relative">
  {#if mawaqeet_data && !show_input}
    <div out:slide={{duration:500}}>
      <!-- Render data -->
      <div in:slide={{duration:500,delay:800}} class="w-full rounded-2xl bg-primary py-6 px-8 flex flex-col relative">
        <img on:click={change_city} class="absolute cursor-pointer top-6 left-8 w-7 md:w-8" src="./imgs/pin.svg" alt="pin"/>
        <h1 class="text-white text-2xl md:text-4xl">{format_time(time)}</h1>
        <h1 class="text-white text-5xl md:text-6xl my-4">{mawaqeet_data.city}</h1>
        <h1 class="text-white opacity-80 text-xl md:text-2xl mb-1">{mawaqeet_data.hijri}</h1>
        <h1 class="text-white opacity-80 text-xl md:text-2xl mb-1">{mawaqeet_data.date}</h1>
      </div>

      <!-- timings table -->
      <div in:slide={{duration:500,delay:1500}} class="w-full mt-4 rounded-2xl bg-white py-6 px-8 flex flex-col relative">
        {#each Object.keys(arabic_times) as key}
          <div class="w-full flex items-center border-b border-primary border-opacity-20 py-4 last:border-b-0 justify-between">
            <h1 class="text-primary text-xl md:text-2xl flex-1 text-right">{get_arabic_time(key)}</h1>
            <h1 class="text-primary text-xl md:text-2xl w-20 text-right">{time_convert(mawaqeet_data?.timings[key])}</h1>
          </div>
        {/each}
      </div>
    </div>
  {:else}
    <div in:blur={{duration: 500,delay:600}}>
      <!-- Search input -->
      {#if show_input && !is_getting_data}
        <div out:blur={{duration: 500}} in:blur={{duration: 500}}>
          <h1 class="text-3xl md:text-4xl text-white mb-4 text-center">مواقيت الصلاة</h1>
          <Input
            placeholder='اسم المدينة' 
            value={search}
            loading={is_loading}
            on_input={search_for_city} 
          />
          <!-- display request error -->
          <h1 class="text-xl text-white mt-2 text-right">{error}</h1>
        </div>
      {/if}
      <!-- Select cities -->
      {#if search && show_input && !is_loading}
        <div in:slide={{duration: 200}} out:slide={{duration: 200}} class={`absolute top-28 md:top-[7.5rem] w-full bg-white mt-3 transition-all rounded-2xl ${cities.length >= 10 ? 'h-72' : 'h-auto'} overflow-y-auto p-2 z-50`}>
          
          {#if cities?.length}
            <LazyList
              data={cities}
              itemsPerRender={10}
              let:item={city}
              defaultLoading={false}
            >
              <h1 on:click={() => choose_city(city)} class="px-6 py-2 mt-1 rounded-lg select-none whitespace-nowrap overflow-hidden text-ellipsis hover:bg-gray-100 text-primary cursor-pointer">{city}</h1>
              <img slot="loading" src="/imgs/loading.svg" alt="loading" width="18"/>
            </LazyList>
          {:else}
            <h1 class="px-6 py-2 mt-1 rounded-lg select-none whitespace-nowrap overflow-hidden text-ellipsis text-primary">
              لا يوجد مدينة بهذا الاسم
            </h1>
          {/if}

        </div>  
      {/if}
    </div>
  {/if}
  </div>
</main>

<!-- Full screen loading -->
{#if is_getting_data}
  <Loading/>
{/if}

<!-- Developed by and github button -->
{#if !is_getting_data && show_developed_by}
  <Github/>
  <h4 in:blur={{duration:1000,delay:500}} class="fixed whitespace-nowrap bottom-8 left-1/2 text-white text-lg md:text-2xl -translate-x-1/2 z-50"><a href="" target="_blank" class="hover:underline"></a></h4>
{/if}
