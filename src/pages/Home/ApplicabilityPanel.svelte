<script>
    import * as IDS from "$src/modules/api/ids.svelte.js";
    import FacetEditor from './FacetEditor.svelte';

    let { activeTab } = $props();

    let activeDocument = $derived(IDS.Module.activeDocument ? IDS.Module.documents[IDS.Module.activeDocument.id] : null);
    let activeSpecification = $derived(activeDocument && IDS.Module.activeDocument?.specification !== null && activeDocument.specifications?.specification ? 
        activeDocument.specifications.specification[IDS.Module.activeDocument.specification] : null);

    async function addFacet (facetType) {
        if (!activeSpecification) return;
        
        await IDS.createFacet(
            IDS.Module.activeDocument.id, 
            IDS.Module.activeDocument.specification, 
            "applicability", 
            facetType
        );
    }
    
    async function removeFacet(facetType, facetIndex) {
        if (!activeSpecification) return;
        
        await IDS.deleteFacet(
            IDS.Module.activeDocument.id, 
            IDS.Module.activeDocument.specification, 
            "applicability", 
            facetType,
            facetIndex
        );
    }
</script>

<div class="restrictions-panel">
    <div class="restrictions-header">
        <h3>Applicability</h3>
        <div class="restriction-buttons">
            <button class="btn-small" onclick={() => addFacet('entity')}>+ Entity</button>
            <button class="btn-small" onclick={() => addFacet('attribute')}>+ Attribute</button>
            <button class="btn-small" onclick={() => addFacet('property')}>+ Property</button>
            <button class="btn-small" onclick={() => addFacet('material')}>+ Material</button>
            <button class="btn-small" onclick={() => addFacet('classification')}>+ Classification</button>
            <button class="btn-small" onclick={() => addFacet('partOf')}>+ Part Of</button>
        </div>
    </div>
    <div class="restrictions-list">
        {#if activeSpecification?.applicability}
            {#each Object.entries(activeSpecification.applicability) as [facetType, facets]}
                {#if facetType !== "@minOccurs" && facetType !== "@maxOccurs"}
                    {#each facets as facet, index}
                        <FacetEditor 
                            {facet} 
                            {facetType} 
                            activeTab="applicability" 
                            {removeFacet} 
                            {index} 
                        />
                    {/each}
                {/if}
            {/each}
        {/if}
    </div>
</div>