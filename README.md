# bin.blast
#' Bin reads into taxa based on blast results
#'
#' @param blastfile blast tabular output, e.g. -outfmt 6. Can have any columns, but must have:
#'     qcovs, evalue, staxid, qseqid, pident
#' @param headers A space-separated string of the header names of \code{blastfile}.
#'     Usually this is the same as the blast command used.
#' @param ncbiTaxDir The full path to the directory containing ncbi taxonomy. 
#' @param obitaxdb The full path to the obitools-formatted ncbi taxonomy. 
#' @param out specify file to output results
#' @param min_qcovs The minimum query coverage for all hits
#' @param max_evalue The maximum evalue for all hits
#' @param top The percentage pident to consider hits. i.e. For each query find the top hit pident (% identity),
#'     then find all pidents within "top" of this value, discard others - e.g. if top set at 1, and top hit for
#'     query A has pident=99.8, then any hits to query A with pident above 98.8 are kept
#' @param spident The minimum pident for binning hits at species level
#' @param gpident The minimum pident for binning hits at genus level
#' @param fpident The minimum pident for binning hits at family level
#' @param abspident An absolute pident for binning hits above family level. Hits below this threshold will be
#'     excluded completely
#'
#' @return A tab-separated file consisting of ESV name and associated taxonomic path
#' @note It works like this:
#' \itemize{
#'     \item 1. Apply min_qcovs (query coverage) to whole table
#'     \item 2. Apply max_evalue (blast evalue) to whole table
#'     \item 3. For each query find the top hit pident (% identity),
#'     then find all pidents within "top" of this value, discard others - e.g. if top set at 1,
#'     and top hit for query A has pident=99.8, then any hits with pident above 98.8 are kept.
#'     \item 4. Add taxon path to all remaining hits
#'     \item 5. For species-level binning: first remove hits that do not have species-level information
#'     (i.e. where database entries were only labelled at genus or higher), then remove hits with pident<spident.
#'     Then, for each query, find the lowest-common-ancestor (lca) of the remaining hits.
#'     \item 6. Repeat above for genus and family level binning
#'     \item 7. Do a final binning for hits that had taxa identified at higher-than-family level
#'     \item 6. Final report=if a query had species-level lca, use that. If not, use genus. If not genus, use family.
#'     If not family use absolute.
#'
#' @examples
#' blastfile<-"blast.results.txt"
#' headers<-"qseqid sacc sseqid sallseqid sallacc bitscore score staxid staxids sscinames scomnames salltitles length pident qcovs"
#' ncbiTaxDir<-"/Documents/TAXONOMIES/"
#' obitaxdb<-"/Documents/obitax_26-4-19"
#' 
