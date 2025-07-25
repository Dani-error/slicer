<script lang="ts">
    import type { Node } from "@run-slicer/asm";
    import type { UTF8Entry } from "@run-slicer/asm/pool";
    import { Table, TableCell, TableHead, TableRow } from "$lib/components/ui/table";
    import { ElementType, formatMod } from "@run-slicer/asm/analysis/disasm";
    import { AttributeType, Modifier, Version } from "@run-slicer/asm/spec";
    import type { SignatureAttribute, SourceFileAttribute } from "@run-slicer/asm/attr";

    interface Props {
        node: Node;
    }

    let { node }: Props = $props();

    const signatureAttr = node.attrs.find((a) => a.type === AttributeType.SIGNATURE);
    const sourceFileAttr = node.attrs.find((a) => a.type === AttributeType.SOURCE_FILE);

    const version = Version[node.major]?.substring(2)?.replaceAll("_", ".") || `${node.major - 44}?`;
    const name = (node.pool[node.thisClass.name] as UTF8Entry).string;
    const mods = formatMod(
        node.access,
        (node.access & Modifier.INTERFACE) !== 0 ? ElementType.INTERFACE : ElementType.CLASS
    );
    const superName = node.superClass ? (node.pool[node.superClass.name] as UTF8Entry).string : null;
    const interfaces = node.interfaces.map(({ name }) => (node.pool[name] as UTF8Entry).string);
    const signature = (signatureAttr as SignatureAttribute)?.signatureEntry?.string;
    const sourceFile = (sourceFileAttr as SourceFileAttribute)?.sourceFileEntry?.string;
</script>

<Table>
    <TableRow>
        <TableHead>Magic</TableHead>
        <TableCell>
            <span class="font-mono tracking-tight">
                0x{node.magic.toString(16)}
            </span>
            ({node.magic})
        </TableCell>
    </TableRow>
    <TableRow>
        <TableHead>Minor version</TableHead>
        <TableCell class="font-mono tracking-tight">{node.minor}</TableCell>
    </TableRow>
    <TableRow>
        <TableHead>Major version</TableHead>
        <TableCell>
            <span class="font-mono tracking-tight">
                {node.major}
            </span>
            ({version})
        </TableCell>
    </TableRow>
    <TableRow>
        <TableHead>Name</TableHead>
        <TableCell class="break-anywhere font-mono tracking-tight whitespace-normal">{name}</TableCell>
    </TableRow>
    <TableRow>
        <TableHead>Modifiers</TableHead>
        <TableCell>
            <span class="break-anywhere font-mono tracking-tight">
                {mods || "<none>"}
            </span>
            ({node.access})
        </TableCell>
    </TableRow>
    <TableRow>
        <TableHead>Super</TableHead>
        <TableCell class="break-anywhere font-mono tracking-tight">{superName || "<none>"}</TableCell>
    </TableRow>
    <TableRow>
        <TableHead>Interfaces</TableHead>
        <TableCell class="break-anywhere font-mono tracking-tight whitespace-normal">
            {#if interfaces.length > 0}
                {#each interfaces as iff, i}
                    {iff}
                    {#if i !== interfaces.length - 1}<br />{/if}
                {/each}
            {:else}
                {"<none>"}
            {/if}
        </TableCell>
    </TableRow>
    <TableRow>
        <TableHead>Signature</TableHead>
        <TableCell class="break-anywhere font-mono tracking-tight whitespace-normal">{signature || "<none>"}</TableCell>
    </TableRow>
    <TableRow>
        <TableHead>Source file</TableHead>
        <TableCell class="break-anywhere font-mono tracking-tight whitespace-normal">{sourceFile || "<none>"}</TableCell
        >
    </TableRow>
</Table>
