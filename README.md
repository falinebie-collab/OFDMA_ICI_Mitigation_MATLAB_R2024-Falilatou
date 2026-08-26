function scenarios = scenarioConfig()
% scenarioConfig
% Définit les scénarios de simulation S0 à S13.
%
% Sortie :
%   scenarios : tableau de structures décrivant chaque scénario.
%
% Champs de chaque scénario :
%   id          : identifiant numérique du scénario
%   name        : nom du scénario
%   scheme      : stratégie d'allocation ou de mitigation
%   receiver    : type de récepteur
%   useHetNet   : activation ou non de la topologie HetNet
%   description : description du scénario

%% Noms des scénarios

names = {
    'S0_SANITY_SINGLE_CELL'
    'S1_BASELINE_FRF1_MRC'
    'S2_STRICT_FFR'
    'S3_SOFT_FREQUENCY_REUSE'
    'S4_FLEXIBLE_FFR'
    'S5_HEURISTIC_DCA'
    'S6_EICIC_ABS_CRE_HETNET'
    'S7_RANDOMIZATION_SCRAMBLING_INTERLEAVING_HOPPING'
    'S8_MMSE_IRC'
    'S9_IDMA_ITERATIVE'
    'S10_COMP_JT'
    'S11_RL_DCA_DQN'
    'S12_RESOURCE_CONSTRAINED_DEPLOYMENT'
    'S13_FIXED_EDGE_USER_PRIORITY'
};

%% Stratégies associées

schemes = {
    'SANITY'
    'FRF1'
    'FFR'
    'SFR'
    'FLEXFFR'
    'DCA'
    'EICIC'
    'RANDOMIZATION'
    'IRC'
    'IDMA'
    'COMP'
    'RL_DCA'
    'RESOURCE_CONSTRAINED'
    'FIXED_EDGE_PRIORITY'
};

%% Récepteurs associés

receivers = {
    'MRC'
    'MRC'
    'MRC'
    'MRC'
    'MRC'
    'MRC'
    'MRC'
    'MRC'
    'IRC'
    'IDMA'
    'MRC'
    'MRC'
    'MRC'
    'MRC'
};

%% Activation de la topologie HetNet

useHetNet = [
    false  % S0
    false  % S1
    false  % S2
    false  % S3
    false  % S4
    false  % S5
    true   % S6
    false  % S7
    false  % S8
    false  % S9
    false  % S10
    false  % S11
    false  % S12
    false  % S13
];

%% Vérification de la cohérence des tableaux

numScenarios = numel(names);

if numel(schemes) ~= numScenarios
    error( ...
        'scenarioConfig:InvalidSchemeCount', ...
        ['Le nombre de stratégies (%d) doit correspondre au ' ...
         'nombre de scénarios (%d).'], ...
        numel(schemes), ...
        numScenarios);
end

if numel(receivers) ~= numScenarios
    error( ...
        'scenarioConfig:InvalidReceiverCount', ...
        ['Le nombre de récepteurs (%d) doit correspondre au ' ...
         'nombre de scénarios (%d).'], ...
        numel(receivers), ...
        numScenarios);
end

if numel(useHetNet) ~= numScenarios
    error( ...
        'scenarioConfig:InvalidHetNetCount', ...
        ['Le nombre de valeurs useHetNet (%d) doit correspondre ' ...
         'au nombre de scénarios (%d).'], ...
        numel(useHetNet), ...
        numScenarios);
end

%% Construction des structures de scénarios

scenarios = repmat( ...
    struct( ...
        'id', [], ...
        'name', '', ...
        'scheme', '', ...
        'receiver', '', ...
        'useHetNet', false, ...
        'description', ''), ...
    numScenarios, ...
    1);

for k = 1:numScenarios

    % L'indice MATLAB commence à 1, mais les scénarios commencent à S0.
    scenarios(k).id = k - 1;

    scenarios(k).name = names{k};

    scenarios(k).scheme = schemes{k};

    scenarios(k).receiver = receivers{k};

    scenarios(k).useHetNet = useHetNet(k);

    scenarios(k).description = ...
        scenarioDescription(names{k});
end

end


function txt = scenarioDescription(name)
% scenarioDescription
% Retourne la description correspondant au nom du scénario.

switch upper(char(name))

    case 'S0_SANITY_SINGLE_CELL'

        txt = ...
            ['Contrôle mono-cellule sans ICI, utilisé pour valider ' ...
             'le bruit, le path loss, le SINR et le débit.'];

    case 'S1_BASELINE_FRF1_MRC'

        txt = ...
            ['Référence multicellulaire FRF=1 avec récepteur MRC ' ...
             'et niveau maximal d''interférences intercellulaires.'];

    case 'S2_STRICT_FFR'

        txt = ...
            ['FFR stricte avec ressources centrales partagées et ' ...
             'sous-bandes de bord protégées par une réutilisation 3.'];

    case 'S3_SOFT_FREQUENCY_REUSE'

        txt = ...
            ['SFR avec puissance réduite sur les ressources centrales ' ...
             'et puissance renforcée sur les ressources de bord.'];

    case 'S4_FLEXIBLE_FFR'

        txt = ...
            ['FFR flexible adaptant la répartition des ressources ' ...
             'selon la charge et la proportion d''UE de bord.'];

    case 'S5_HEURISTIC_DCA'

        txt = ...
            ['Allocation dynamique heuristique des RB selon la qualité ' ...
             'du canal et les interférences estimées, sans collision OFDMA.'];

    case 'S6_EICIC_ABS_CRE_HETNET'

        txt = ...
            ['Topologie HetNet macro-pico avec ABS, réduction de ' ...
             'puissance macro et biais Cell Range Expansion.'];

    case 'S7_RANDOMIZATION_SCRAMBLING_INTERLEAVING_HOPPING'

        txt = ...
            ['Randomisation des interférences par scrambling cellule, ' ...
             'interleaving et saut fréquentiel.'];

    case 'S8_MMSE_IRC'

        txt = ...
            ['Récepteur MMSE-IRC permettant de réduire les ' ...
             'interférences reçues, particulièrement pour les UE de bord.'];

    case 'S9_IDMA_ITERATIVE'

        txt = ...
            ['IDMA avec détection ESE/SISO simplifiée et mécanisme ' ...
             'd''annulation itérative des interférences.'];

    case 'S10_COMP_JT'

        txt = ...
            ['CoMP Joint Transmission avec cluster de coordination ' ...
             'et précodage MRT simplifié.'];

    case 'S11_RL_DCA_DQN'

        txt = ...
            ['Allocation DCA pilotée par un agent DQN choisissant une ' ...
             'politique parmi six actions d''allocation sans collision.'];

    case 'S12_RESOURCE_CONSTRAINED_DEPLOYMENT'

        txt = ...
            ['Déploiement à ressources contraintes utilisant une ' ...
             'stratégie SFR avec ordonnancement semi-statique par blocs ' ...
             'de RB, CSI dégradée (coordination inter-cellulaire moins ' ...
             'précise que le SFR standard) et signalisation X2 nulle.'];

    case 'S13_FIXED_EDGE_USER_PRIORITY'

        txt = ...
            ['Politique heuristique fixe donnant la priorité aux ' ...
             'utilisateurs de bord. Elle correspond à l''action 2 du DQN ' ...
             'et sert de référence pour évaluer le gain réel de l''apprentissage.'];

    otherwise

        error( ...
            'scenarioConfig:UnknownScenario', ...
            'Aucune description définie pour le scénario : %s.', ...
            char(name));
end

end
