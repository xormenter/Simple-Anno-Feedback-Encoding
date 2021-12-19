# Simple-Anno-Feedback-Encoding
Converts a simplified feedback definition for Anno 1800 into .cf7 files.

## Requirements
- Python3 with lxml

## Usage
``python3 simple_anno_feedback_encoding_to_cf7 -i example_input.xml``
Converts it to cf7. Then convert it to .fc.

## The simplified feedback:
Anno 1800 .fc files are complicated, no one really knows how exactlythey work and you need to keep a lot of things in mind to not mess up, so they scare people, which is bad. 
The new format is less powerful, but still allows you to describe sequences of actors moving from waypoint to waypoint and showing idle animations, which is the number one use case.

The following snippets defines a feedback loop where cows walks between two spots and eats some grass.
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
            <MaxActorCount>2</MaxActorCount>
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

