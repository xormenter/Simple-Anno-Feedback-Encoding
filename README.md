# Simple-Anno-Feedback-Encoding
Converts a simplified feedback definition for Anno 1800 into .cf7 files.

## Requirements
- Python3 with lxml
- I suggest using my blender addon for positioning the dummies. https://github.com/xormenter/Blender-Anno-.cfg-Import-Addon/blob/main/README.md

## Usage
``python3 simple_anno_feedback_encoding_to_cf7 -i example_input.xml``
Converts it to cf7. Then convert it to .fc.

## The simplified feedback:
Anno 1800 .fc files are complicated, no one really knows how exactly they work and you need to keep a lot of things in mind to not mess up, so they scare people, which is bad. 
The new format is less powerful, but still allows you to describe sequences of actors moving from waypoint to waypoint and showing idle animations, which is the number one use case.

### Example
Let's say we want to have a cow that moves between 2 locations and eats some grass at both of them. 
Firstly, we need the GUID of the cow feedback actor, which is 1000135. For readability, we can define an alias ``GUID_COW`` in the ``GUIDNames`` section. This is optional though.
Then, before we tell the cow to move, we must define the "waypoints", which are called Dummy in Anno. Actually, not all dummies in .fc files are such waypoints (all Dummies in the FireDummy group are fire spawning places), but most are. So we add two dummies here, ``walking_cow_1`` and ``walking_cow_2`` (and put them in a similarly named dummy group). Please note that all dummies need a unique name! The most important other attributes of a dummy are ``Position``, ``RotationY`` and ``HeightAdaptationMode`` (1 -> adapt to terrain height; 0 -> always at the same height). But I'd suggest to use a tool for placing the dummies.

Then we come to the ``FeedbackConfigs`` section. Each ``FeedbackConfig`` entry in here corresponds to one animation sequence. Here we must specify that we want to use the cow model by adding it in the ``GUIDVariationList`` (we could add multiple different guid entries here). We want 1 cow and it should be scaled at 0.5 (seems to be the default for a lot of actors). We must also specify where our cow should start, lets say at the  ``walking_cow_1`` dummy.
Finally, we can define our animation sequence in ``SequenceElements``: Here we can add ``IdleAnimation, TimedIdleAnimation, TurnAngle, TurnAtDummy, Walk, Wait`` sections which are played back in the order in which we define them here. First we want the cow to eat, so we use an ``IdleAnimation`` with the ``idle01`` animation (eating grass). (To get a list of possible animations, run the program with the option -s. Note that not all animations are valid for all actors, if they are invalid the actor will simply vanish.) Then, the cow should move to the next waypoint, so we use a ``Walk`` section where we specify the next dummy ``walking_cow_2``. A ``SpeedFactorF`` of zero corresponds to the default speed. Then we add another idle animation and move back to the start, and we'e done.

Here's the full code: 
```xml
<SimpleAnnoFeedbackEncoding>
    <GUIDNames>     <!-- (Optional) Aliases for readability -->
        <Item>
            <GUID>1000135</GUID>
            <Name>GUID_Cow</Name>
        </Item>
    </GUIDNames>
    <DummyGroups>
        <DummyGroup>
            <Name>walking_cow</Name>
            <Dummy> 
                <!-- A dummy is a "waypoint" for the feedback actors. It can be used independently by different FeedbackConfigs -->
                <Name>walking_cow_1</Name>
                <Position>
                  <x>-0.744948</x><y>0.788913</y><z>0.738752</z>
                </Position>
                <Orientation>
                  <w>0.704123</w><x>0.064886</x><y>0.064886</y><z>0.704123</z>
                </Orientation>
                <Extents>
                  <x>0.400000</x><y>0.400000</y><z>0.400000</z>
                </Extents>
                <RotationY>0.000000</RotationY>
                <HeightAdaptationMode>1</HeightAdaptationMode>
            </Dummy>
            <Dummy>
                <Name>walking_cow_2</Name>
                <Position>
                  <x>1.744948</x><y>2.788913</y><z>3.738752</z>
                </Position>
                <Orientation>
                  <w>1.000000</w><x>0.000000</x><y>0.000000</y><z>0.000000</z>
                </Orientation>
                <Extents>
                  <x>1.400000</x><y>1.400000</y><z>1.400000</z>
                </Extents>
                <RotationY>3.141529</RotationY>
                <HeightAdaptationMode>1</HeightAdaptationMode>
          </Dummy>
        </DummyGroup>
    </DummyGroups>
    <FeedbackConfigs> <!-- We can have an arbitrary number of FeedbackConfigs here -->
        <FeedbackConfig>
            <Description>a cow walks around and does stuff</Description>
            <GUIDVariationList>
                <GUID>GUID_Cow</GUID>
            </GUIDVariationList>
            <IsAlwaysVisibleActor>0</IsAlwaysVisibleActor>
            <ActorCount>1</ActorCount>
            <MaxActorCount>1</MaxActorCount>
            <CreateChance>100</CreateChance>
            <MultiplyActorByDummyCount/>
            <Scale>
                <m_MinScaleFactor>0.500000</m_MinScaleFactor>
                <m_MaxScaleFactor>0.500000</m_MaxScaleFactor>
            </Scale>
            <DefaultStateDummy>walking_cow_1</DefaultStateDummy>
            <SequenceElements>
                <!-- A sequence consists of an arbitrary number of IdleAnimation, TimedIdleAnimation, TurnAngle, TurnAtDummy and Walk elements. They are played back in this order. -->
                <IdleAnimation>
                    <m_IdleSequenceID>idle01</m_IdleSequenceID> <!-- Animation: Cow eating. To get a list of valid sequences, run the program with the flag -s.-->
                    <MinPlayCount>1</MinPlayCount>
                    <MaxPlayCount>3</MaxPlayCount>
                </IdleAnimation>
                <Walk> <!-- Walk from the current position to the TargetDummy -->
                    <WalkSequence>walk01</WalkSequence> <!-- Animation: Cow walking -->
                    <TargetDummy>walking_cow_1</TargetDummy>
                    <SpeedFactorF>1.000000</SpeedFactorF>
                </Walk>
                <TimedIdleAnimation> <!-- Time in ms -->
                    <m_IdleSequenceID>idle01</m_IdleSequenceID>
                    <MinPlayTime>1000</MinPlayTime>
                    <MaxPlayTime>3000</MaxPlayTime>
                </TimedIdleAnimation>
                <Walk> 
                    <WalkSequence>walk01</WalkSequence>
                    <TargetDummy>walking_cow_2</TargetDummy>
                    <SpeedFactorF>0.000000</SpeedFactorF>
                </Walk>
            </SequenceElements>
        </FeedbackConfig>
    </FeedbackConfigs>
</SimpleAnnoFeedbackEncoding>
```

